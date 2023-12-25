---
layout: post
category: posts
title: "Uncovering the Secrets : The Potential of Web Archive in Bug Bounty Programs"
---

![banner](https://miro.medium.com/v2/resize:fit:1100/format:webp/1*7tGxwHgIUzHlLJqIgWXAJA.png){:.iod}

### The Infamous WayBack Machine
*"According to ChatGPT, The Wayback Machine is a digital archive of the World Wide Web, maintained by the Internet Archive. It is a valuable tool that allows users to access and browse archived versions of websites as they appeared at different points in time. The Wayback Machine captures and stores snapshots of web pages, creating a historical record of the internet."*

The WayBack Machine was initially designed with the intention of serving a noble purpose by preserving valuable knowledge and information from web pages that have been removed or deleted. However, as it fulfills this objective, the WayBack Machine also records and retains the URLs and any sensitive information mentioned within those URLs.

### Unraveling Vulnerabilities Wide Open
Although the WayBack Machine logs various aspects for its intended purpose, it also inadvertently captures vulnerabilities present in applications and sensitive information that was never intended to be made public. These may include confidential details like discount coupons, various types of tokens, hidden `GET` or query parameters, and similar information.

If utilized correctly, these seemingly minor details can potentially lead bug bounty hunters to discover critical and groundbreaking vulnerabilities.

To gain a better understanding, let’s examine a few examples I have come across within the past six months:

### Password leakage
As we are aware, in approximately one out of every three SaaS applications or service provider applications, APIs are implemented to facilitate programmatic access to their data, In order to grant access to the appropriate resources, these applications typically require an *authentication and authorization flow*[^1]. While many applications offer login options with Google or OAuth, some still rely on their own password authentication or API tokens/keys. Typically, these credentials are passed as `POST` parameters. However, in the case of secret.app, they were transmitted as `GET` or query parameters. Furthermore, this particular application demanded both a username and password for authentication alongside OAuth. 

```
/oauth/v2/token?client_id=<ID>&client_secret=<secret>&grant_type=password&scope=user&username=community@victim.com&password=<password>
```

Evidences from API Documentation

```
curl --location 'https://api.secret.app/oauth/v2/token' \
--form 'client_id="{{client_id}}"' \
--form 'client_secret="{{client_secret}}"' \
--form 'grant_type="password"' \
--form 'scope="user"' \
--form 'username="{{username}}"' \
--form 'password="{{password}}"'
```

During the OAuth or authentication process, the username and password were provided through query parameters, and these details were captured and recorded by the WayBack Machine.

### Authentication Bypass Detection
In this particular scenario, the vulnerable application is commonly susceptible to *CVE-2023–0328*[^2] or a similar vulnerability. In simpler terms, this vulnerability enables an attacker to gain access to unauthorized resources by exploiting a lack of capability checks and handling of the `Authorization` header. 

```
https://api.secret.app/account/xxxx/members?members[fields][]=firstName&member_data[fields][]=lastName&membet_data[fields][]=organizationName&sid=xxxxxx
```

By visiting the links that have been logged on archive.org (WayBack / WebArchive), it is possible to identify and detect this vulnerability.

### User Personal Information Leakage
This particular issue is commonly observed in applications that utilize *Shopify*[^3] to create an online store for selling products. In such cases, the checkout and orders URL paths are often not adequately secured or configured to require authentication. As a consequence, when an actor visits the links stored in archive.org, they gain unauthorized access to the user’s personal information, including the billing details. `https://shop.secret.app/xxxxx/orders/<SSID>`

### Unauthorized Access to User Preferences
This issue arises when an application fails to enforce authentication through request headers or cookies, thereby granting access to any actor who possesses the preference link containing the user ID or token. In my case, the application did not require cookie or header authentication, enabling the actor to modify user preferences solely by accessing a specially crafted URL, which was logged in archive.org. `https://pref.secret.app/unsubscribe?unsubscribe=1&uid=<UUID>`

### Key Points
As demonstrated by the examples provided above, the targeted application can be susceptible to various vulnerabilities. It is crucial to comprehend the underlying logic behind any functionality that an application allows. The vulnerabilities illustrated are not exhaustive, and with the help of the WayBack Machine, bug bounty hunters can uncover numerous intriguing vulnerabilities such as Cross-Site Scripting (XSS), Insecure Direct Object Reference (IDOR), and Remote Code Execution (RCE).

### Easy Hunt
While a significant number of individuals depend on the command line interface to retrieve information and access logged URLs from the WayBack Machine, here is an alternative method available for obtaining the same data through a web browser interface.

```
https://web.archive.org/cdx/search/cdx?url=*.secret.app/*&output=text&fl=original&collapse=urlkey
```

### Solution
While the recommended approach is to identify and address each vulnerability individually, organizations can opt for a simple fix by utilizing a specific entry in their robots.txt file to prevent or block the WayBack Machine from crawling their applications.

```
User-agent: la_archiver
Disallow: /
```

To gain further insight into the solution, I recommend reading an excellent article on the topic available at *reputationx.com*[^4].

I hope you have found this post informative and learnt something new from it.<br/>
Thanks!


---
{: data-content="footnotes"}

[^1]: [Authentication and Authorization flow](https://www.rfc-editor.org/rfc/rfc6749){:target="_blank"}
[^2]: [CVE-2023–0328](https://www.cve.org/CVERecord?id=CVE-2023-0328){:target="_blank"}
[^3]: [Shopify](https://www.shopify.com/){:target="_blank"}
[^4]: [reputationx.com](https://blog.reputationx.com/block-wayback-machine){:target="_blank"}