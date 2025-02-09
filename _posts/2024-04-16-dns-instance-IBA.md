---
layout: post
category: posts
title: "Hacking DNS Instance Applications Inside-Out"
description : "DNS Instance Application Testing Guide"
tags : "Hacking, Penetration Testing, Bug Bounty, DNS Hacking, Misconfigurations, DNS Implementation"
tags : "dns, hacking, penetration testing, bug bounty, dns hacking, dns implementation"
---

You may have already seen an application which provides you with a subdomain on the application domain based on your organization or team name, I call these applications, Instance Based Applications or IBA. There are many examples for these type of applications, such as Okta.com, Circle.so and github.com.
```
mr23r0.okta.com | mr23r0.github.io
```

But there's a difference in these applications internally as well as externally, some gives you ability to access your data and manage it through a custom organization subdomain and others just allow you to serve that data on organization subdomain, like okta gives you ability to manage your data through a subdomain and github just allows you publish your data on a subdomain, while using `.io` domain to differentiate between the main application and published data.

<span style="color:blue"><b>Why do that? why not just use simple centralized subdomain like `app`, `portal` or something?</b></span><br/>
The answer to all these questions lies into the _psychology_ of the customer and the vendor, people have tendency to stick to places where they get some special treatment, additionally by receving a custom subdomain customers think that its isolated from the central application and they're far away from the _Cyber Threats_.

<span style="color:blue"><b>How much of this asumption is true? [Based on my research]</b></span><br/>

In reality there's no way possible to create a sudomain on the fly (correct me if I'm wrong) and create a tenant specific copy of the application, although it's possible for the databases, which is called multi-tenancy[^1]
What they do is, they point the wildcard domain `*.mr23r0.app` to the main IP address and serve the same application on any subdomain [virtual] created. See the below code to understand it better :
```
server {
    listen       80;
    server_name  app.mr23r0.app  *.mr23r0.app;
    root /path_to_your_site/public;
    passenger_enabled on;
    rails_env development;
}
```
So The application isn't offering anything special to customers, but the illustion of giving custom special subdomains, which in reality doesn't make any difference or enhances the security of the application, since every other thing in the backend is going through the same pipe or plate.
![DNS-Instance]({{ site.baseurl }}/assets/img/dns-instance.png)

In the last handful of months, I had the chance to learn and test these IBA applications, and there are some major security concerns that could be found in these applications, such as 
- Insecure Direct Object Reference to Add yourself or the resource to any Organization
- Organization Infinite Loop
- Forced Claim to Organizations
- Claiming Other's Access Token

<span style="color:blue"><b>Insecure Direct Object Reference to Add yourself to any Organization</b></span><br/>
Whenever you land on these type of Applications, On the registration page these applications ask you to create the resource, before the organization (seen in event management, site management and documentation applications) and after that the resource is assigned to the newly created organization.<br/> As you may have already got the idea, that this workflow of doing things is very prone to misconfigurations and weak implementation.

_This example is from my test on an Event Hosting Application, which I discovered for the third time in this category of applications (IBA)_

On the registration, the application asks the visitor to create an event before moving ahead, the visitor does it and gets the `eventId` in response, nothing too suspicious, in the next onboarding step application asks the user to create his/her account with all the best practices implemented but in the last two steps the application asks the user to create and organization but as soon as the `orgId` is returned in the repsponse, the application makes a `PATCH` request[^2] to the backend to add the `userId` along with the `eventId` to the organization `/api/v3/orgAdditionOperation` with the json body 
```
{
    "orgId":"UUID2",
    "user":{
        "id":"",
        "eventId":Integer
        }
}
```
the only to handle this properly is to use a flag of some sort to detect if the `orgId` organization is newly created or the old one, which can prevent the malicous attackers from joining the already active organization, but there wasn't such implementation.

<span style="color:blue"><b>Organization Infinite Loop</b></span><br/>
During the onboarding process of these applications, you get the option to select and claim a unique subdomain to access your application, the http request may look something like this 
```
PUT /api/v3/orgOperation
Host:
<headers>

{
    "subdomain":"mr23r0"
}
```
Its important to understand that, some applications only allow you to do this only one time and no way to change the sudomain aferwards but other applications allow you to change this afterwards as well.
The interesting thing is, that in the example server config, the application has used a wildcard, therefore the application should also respond to non claimable subdomains like `www`, `docs` and `crm` etc, which may cause an internal server conflict since the DNS Server[^3] would have both, static application address and the main application address. to prevent this the developers implement a blocklist of subdomains that could not be claimed, and rejected if requested, But this is a bit complex to handle and people are lazy.
![DNS-Instance]({{ site.baseurl }}/assets/img/assign-logic.png)

In my case people weren't lazy and did a secure implementation on the _onboarding process_, but this onboarding process is done by a visitor, so there should be two different components to handle this, first on the onboarding and the second on the organization administration settings, In my case the request from the administrative settings looked like this, and didn't had backend verification using a blocklist.
![DNS-Instance]({{ site.baseurl }}/assets/img/bad-assign-logic.png)
```
PATCH /api/v3/orgOperation/admin/UUID2
HOST : 
<headers>

{
    "subdomain":"mr23r0"
}
```
As soon as the new subdomain gets assigned to the organization, the previous subdomain will no longer be a valid one, and the application redirects the administrator to the new subdomain, but the new subdomain is a static site and redirects the user to the designated subdomain, creating a never ending loop. Ultimetly the browser gets `too many redirects` and the organization data will no longer be accessiable.

<span style="color:blue"><b>Force Claim Organizations</b></span><br/>
On the same organization administrative settings request, if someone puts a subdomain which is already in use by some other organization, the application throws an error "subdomain already in use", There's also absense of limit on how frequently you can change the subdomain.<br/>
By following race condition[^4] approach, and sending a handful of unique and an already in use subdomain in a row (4 unique, 6-10 times single already in use subdomain) the application fails to handle all the requests in a short period of time and returns `500 Internal Server Error` but assigns the subdomain to both organizations.


<span style="color:blue"><b>Claiming Other's Access Token</b></span><br/>
Coming back to the application onboarding flow, resource has been created, user account has been created, _without email verification_, as soon as the user and the organization is being linked, the API returns a temporary access token to login the user for the _first time in the organization_, so what's the attack? what can you leverage here, you might be asking this question by now, so read this block once more, focus on the `italic` fonts.
I notice two things :
- Without Email Verification
- Login first time, in newly created organization

If the application doesn't verifies the ownership of the email, before handing-out the access token, it's already bad but, it can be further followed to a much higher risk.<br/>
Since we have this behavior in the application, We can create a new organization with the victim's email, and get the one-time access token, following the ideal flow. Instance Based Applications, while they are internally stand as a centraliezed (sigular) application but use header like referer and orgin to seperate the different DNS instance data, so the token should have resource access assigned in some way, Ideally.
![DNS-Instance]({{ site.baseurl }}/assets/img/dns-token-generation.png)

This can be tested, by trying to use the token on the previously created organization, the token should not allow you to access the previously created organization, since there's no possible way to switch between the organizations using the application UI, user must login into the second instance to access it.
Ultimately, we got the access of the target instance with just by knowing an email address, funny isn't it?

As we wrap up, I want to suggest that you learn whatever interests you and get really good at it. It's always useful in some way, and keep dreaming until you can make those dreams real. Thanks!


---
{: data-content="footnotes"}

[^1]: [Multi-tenancy](https://www.gooddata.com/blog/multi-tenant-architecture/){:target="_blank"}
[^2]: [Requests - HTTP Verbs](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods){:target="_blank"}
[^3]: [DNS Server](https://www.cloudflare.com/learning/dns/what-is-a-dns-server/){:target="_blank"}
[^4]: [Race Condition](https://www.techtarget.com/searchstorage/definition/race-condition){:target="_blank"}