---
layout: post
category: posts
title: "Four(4) tions of Penetration"
---
### Index
- [Introduction](#introduction)
- [Selection](#selection)
- [Detection](#detection)
- [Relation](#relation)

### [Introduction](#introduction)
After my last extensive vulnerability test[^1], I got the taste of site management applications and had a craving to do more of similar kinds. This post will elaborate the target selection, a series of tests and the way I managed to make a good working relationship with the company.<br/>

The site management applications I choose typically include the capability of creating and managing multiple sites in one account and also supports team management.

### [Selection](#selection)
I invested about 8-9 days after which I realised that Google Dorks[^2] are not the best way of finding similar products and applications, then I remembered about [Crunchbase](https://crunchbase.com){:target="_blank"} and how helpful it is in finding similar products and applications.<br/>
I made a list of 12 similar applications and started looking in the archive logs[^3] for these applications.


During the detection process, I found the `email` query parameter in the unsubscribe path and after testing I found that I can change the user email preferences without proper authentication and authorization, therefore I assumed that there is a high possibility of more bugs in the application and I contacted the CTO of the company.

### [Detection](#detection)
As soon as I got a green flag from the CTO, I initiated my tests with creating two accounts in the same team.<br/>

#### Broken Access Control
 After checking small amount of things on the application, I shifted my focus to break the [Multitenancy](https://www.cloudflare.com/learning/cloud/what-is-multitenancy){:target="_blank"} of the application, therefore I deleted one account from the same team while the user was loggedin. I quickly realised that the user's access and the session is not revoked, and the removed user still has all the administrator privileges.

#### Magic Link Reverse Engineering
 After creating an account for the previously removed user, the first time login magic link caught my attention `<instance>.censored.com/editor/login?password_set=y&account=true&a=<victim's username>`<br/>
 So I checked my first account and solved the puzzle of all the query parameters and using the crafted link, I was able to get into the first account without authentication.

#### Account Takeover
 Magic link reverse engineering filled me with a sense of amazement.
 Then I started an extensive test on the registration process, while creating the third account, I noticed a `POST` request

```
POST /signupajax-v3 HTTP/1.1
Host: censored.com
Connection: close
Content-Length: 359
sec-ch-ua: "Not_A Brand";v="8", "Chromium";v="120", "Google Chrome";v="120"
Accept: */*
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
X-Requested-With: XMLHttpRequest
sec-ch-ua-mobile: ?0
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36
sec-ch-ua-platform: "Linux"
Origin: https://censored.com
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://censored.com/signup
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Cookie: <cookies>

siteName=myThirdAccountSite&siteUrl=myThirdAccountSite&evstart=&adminEmail=root@mr23r0.rocks&duplicate=false&hash=<hash>
```
I changed the email to the first account email, received a magic link in response, by using the magic link I was able to get into the first account, not only this I was also able to navigate between account sites and make changes.

### [Relation](#relation)
When I initially contacted the CTO of the company, although he gave me a green flag but specifically mentioned that he has a tight budget and won't be able to provide reward (bounty), but when I submitted the report he appreciated my work and squeezed the company's budget a bit to show their gratitude.


---
{: data-content="footnotes"}

[^1]: [Code flaws leads to Organization Account Takeover](https://mr23r0.github.io/code-flaws-part-1.html){:target="_blank"}
[^2]: [Google Dorks](https://book.hacktricks.xyz/generic-methodologies-and-resources/external-recon-methodology/github-leaked-secrets){:target="_blank"}
[^3]: [The Potential of Web archive in bug bounty programs](https://mr23r0.github.io/uncovering-the-secrets-part-1.html){:target="_blank"}
