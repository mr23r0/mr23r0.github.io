---
layout: post
category: posts
title: "Unlocking the Power of Observation : the front door key 🔑"
---

![banner](https://miro.medium.com/v2/resize:fit:720/format:webp/1*sLr3oSvwjgfx_6N-Mzgv0g.png){:.iod}

We are currently in the era of SaaS applications and startups. SaaS applications have become ubiquitous, serving various purposes, from sending notifications to collecting feedback. There seems to be a SaaS application for virtually every need, and startups are capitalizing on this trend.

For those familiar with *web applications*[^1], the concept is quite clear: applications require users to create accounts to establish user identities and workspaces. Within these workspaces, users can perform tasks and store their relevant information.

The development and maintenance of user registrations and session management can be quite challenging in terms of security. Therefore, it’s not surprising that SaaS services offer user management features that can be easily integrated with just a few clicks. Startups often opt for these solutions as their primary choice. This allows them to allocate their resources and funds toward developing their core solutions, rather than diverting them to the complexities of user management.

I noticed a similar situation during a *penetration test*[^2] conducted on “company.com.” It’s worth noting that “company.com” serves as an alias for the actual application that will be the focus of discussion throughout this article.

While examining the login page, I recognized that it was associated with a SaaS service providing user management features. To gain deeper insights, I routed every request through Burp Suite, with the assumption that I might discover something valuable about the service.

Following several registration processes and login-logout interactions, I decided to inspect the requests within Burp Proxy and the Target tab. To my surprise, I noticed that certain domains and subdomains were making calls to the SaaS application. Given that such calls are typically intended to be executed on the server side, this observation raised suspicions.

![image](https://miro.medium.com/v2/resize:fit:720/format:webp/1*fDKgDM19nqs1NYWrNlbyDg.jpeg){:.iod}

Among the various calls, two in particular from api.saasapp.com caught my attention. Upon closer examination, I discovered that the response from the POST request included an *OAuth token*[^3], with the request containing both a client ID and a secret.

For those who may not be familiar, it’s essential to understand that virtually every SaaS application offering technical and non-technical services provides APIs for programmatic usage. As I delved into the SaaS application’s documentation, I learned that the key and secret serve the purpose of connecting the customer application (company.com) with the SaaS application (saasapp.com), facilitating the sharing of user identities and session tokens.

![image](https://miro.medium.com/v2/resize:fit:720/format:webp/1*d0iAUVZ-8GFNEO7HZcKlrg.jpeg){:.iod}

Upon further exploration of the keys, I found that they possess administrative privileges. Using the generated token associated with these keys, I gained the ability to retrieve user data, make modifications to their information, and even add members with administrative control over the SaaS application account for the application (company.com).

It granted me Administrator access to the *User Management System*[^4], highlighting yet another instance of how expanding the attack surface and meticulously scrutinizing details can uncover creative yet crucial vulnerabilities. I often refer to this approach as “Unlocking the Power of Observation.” This marks the second article in this series.

Before concluding, I would like you to consider this scenario: What if I were unaware of these SaaS applications or the current trends in the development landscape? I might have dismissed it as a “rabbit hole.” Therefore, I encourage you not to limit your learning. Embrace the opportunity to learn about a wide range of topics because knowledge is an asset that never goes to waste — except perhaps for those intricate mathematical calculations we encountered in school ;)

This concludes my discussion on this topic. I hope you’ve gained new insights from this.<br/>
Thank you! 

---
{: data-content="footnotes"}

[^1]: [Web Applications](https://aws.amazon.com/what-is/web-application/#:~:text=A%20web%20application%20is%20software,with%20customers%20conveniently%20and%20securely.){:target="_blank"}
[^2]: [Penetration Testing/Tests](https://www.synopsys.com/glossary/what-is-penetration-testing.html){:target="_blank"}
[^3]: [OAuth Tokens](https://www.rfc-editor.org/rfc/rfc6749){:target="_blank"}
[^4]: [User Management System](https://www.egnyte.com/guides/governance/user-management#:~:text=User%20management%20is%20a%20system,Providing%20users%20with%20authenticated%20access){:target="_blank"}