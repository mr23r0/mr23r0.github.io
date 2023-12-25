---
layout: post
category: posts
title: "Code flaws leads to Organization Account Takeover"
---

![banner](https://miro.medium.com/v2/resize:fit:720/format:webp/1*TXPw0GXprB2NHBoW4-EvYA.png){:.iod}

### the Application Design
The Application was only allowing one Admin/Manager per one organization, but other viewers has to login in order to view the content.<br/>
*Note: After creating an account user cannot change the `organization_name`.*

### Design Flaw/ Insecure Design
While creating an account I saw a very weird parameter in the `POST` request

```
POST /some_ajax/create_event_flow HTTP/1.1
Host: portal.example.com
Connection: close
Content-Length: 663
sec-ch-ua: "Google Chrome";v="105", "Not)A;Brand";v="8", "Chromium";v="105"
Accept: application/json, text/javascript, */*; q=0.01
Content-Type: application/json; charset=UTF-8
X-Requested-With: XMLHttpRequest
sec-ch-ua-mobile: ?0
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/105.0.0.0 Safari/537.36
sec-ch-ua-platform: "Linux"
Origin: https://portal.example.com
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Se;c-Fetch-Dest: empty
Referer: https://portal.example.com/create_event_flow
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Cookie:
{"methodName":"onboard","params":{"some_params":{"some_name":"Account takeover","some_modules":[1]},"user_params":{"name":"User_name","email":"admin@mr23r0.rocks","password":"Password_Secret"},"community_params":{"name":"Organization_name","slug":"testthing-for-ato","c_id":683,"client_time_zone":"Asia/Calcutta"}}}
```

`c_id` noticed it? I hope so, let’s see what is happening in the backend, While trying to register, the application creates a entry with the email address and creates a row in the organization table (if it’s SQL) then our current request goes into the users table and makes a user with a predefined organization.

```
INSERT INTO users (username, password, organization) VALUES ("Jack","123", "organization_name") WHERE c_id='683';
```

So I changed the `c_id` with the victim’s `c_id` and as expected it took me to the victim’s organization. So we got our first bug ;) Design flaw but we can also call it *Horizontal Privilege Escalation*[^1].<br/>
*Note : The Application doesn’t supports multiple Admin/Manager in one organization.*

When I do testing, primarily what I want to achieve is an Account takeover and testing is like a video game for me completing every level one by one… so I started looking for more vulnerabilities, after 15 minutes of search I got it, found a *Stored Cross Site Scripting*[^2] on the `Organization_name` now the problem is how can I change the victim’s `Organization_name`.

I tried to replicate the create an account request and BOOM!! we can send a POST request to `/some_ajax/create_event_flow` no authentication required and also if we put cross site scripting payload in `Organization_name` and change our `c_id` with the victim’s `c_id`, the victim’s `Organization_name` gets changed ;)

![image](https://miro.medium.com/v2/resize:fit:562/format:webp/1*Pkd4zlVh7_ynWK7o1TOR8g.png){:.iod}

We got the victim’s active cookies — but no-one will accept account takeover with active cookies, So I simply checked for *Session Fixation or No Logout*[^3] and I got lucky the application was vulnerable.

Last two vulnerability, the application was taking current password in order to change password but after intercepting the request I found that they’re not sending or checking the current password and there wasn’t any authentication on change email operation.

### Quick wrap up
```
Create an account --> change the "c_id" and add the blind xss payload in the "organization_name"
copy the victim's cookies 
Login as the victim by using the cookies
change the password by using:
           i. Change Password
           ii. Change Email --> Recover password
```

I hope you’ve learned something new from this.<br/>
Thanks!

---
{: data-content="footnotes"}

[^1]: [Horizontal Privilege Escalation](https://heimdalsecurity.com/blog/privilege-escalation/){:target="_blank"}
[^2]: [Stored Cross Site Scripting](https://portswigger.net/web-security/cross-site-scripting/stored){:target="_blank"}
[^3]: [Session Fixation or No Logout](https://owasp.org/www-community/attacks/Session_fixation){:target="_blank"}