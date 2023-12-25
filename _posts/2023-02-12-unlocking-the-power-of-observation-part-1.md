---
layout: post
category: posts
title: "Unlocking the Power of Observation: How Experienced Hackers Stand Out in a Sea of Amateurs"
---

![banner](https://miro.medium.com/v2/resize:fit:720/format:webp/1*CSzOXZh3oguY5uNaFETg0g.jpeg){:.iod}

Hackers need to embrace their superpower in order to become more efficient and meaningful, while doing bug bounty they have to search for weaknesses and also make sure that with their actions the application doesn’t crash.

![image](https://miro.medium.com/v2/resize:fit:720/format:webp/1*7TL7yeUfzC8zLRgDtJcyXA.jpeg){:.iod}

In the interest of time, let’s talk about one feature I saw in the middle of last year.

### Functionality
This functionality gives users the ability to claim a subdomain on the application (website) to create a workspace and work directly on that subdomain. (In simple words, the application allows users to create instances)

### Attack (i) : Account Takeover
I’ve already touched on this, so I suggest you read my previous *article*[^1]. I assume you read this article, so let’s continue.

### Attack (ii) : Account Takeover
Application Flow : When a user tries to sign-up, the application doesn’t requires user to verify the email first and after completing the sign-up process successfully the user gets a “login token” and with the help of this “login token” user can login into his/her account without the password.<br/>
*Note: This token is “One Time Token”*

This is the same application as *the Attack (i)* so again, I created a new subdomain and on the claim subdomain request I replaced my email with the victim’s email and as I don’t know the password of the victim’s email I added “anything” in the password field.

```
{
  "methodName": "onboard",
  "params": {
    "some_params": {
      "some_name": "anything",
      "some_modules": [
        1
      ]
    },
    "user_params": {
      "name": "CanBeAnything",
      "email": "hunterzerocipher@gmail.com",
      "password": "Anything"
    },
    "community_params": {
      "name": "anything",
      "slug": "anything",
      "c_id": 715,
      "client_time_zone": "Asia/Calcutta"
    }
  }
}
```

On the response I got the “login token” and made a browser GET request on the victim’s subdomain with the “login token” and I was logged-in in the victim’s account.

`https://victim.application.com/admin/login?login_token=xxxxxxxxxxx`

In this example what happened is the application doesn’t actually generate the login token with multiple details of the user, it only used the email to generate the login token and as the login token only contains a pass to login as the user without checking the subdomain or the organization, allowing attacker to create login pass with the victim email.

### Attack (iii) : System Conflict
I have to say this example is a bit funny ;)

As the previous example I was trying to create a subdomain in the application and the url was `https://portal.application.com/register` and while login I noticed that the url is `https://login.application.com/admin/login`
So I again tried to create a subdomain “xzcvb” and Intercepted the request in *Burp Suite*[^2] changed the subdomain name to “login” and sent the request and as a surprise I got “200 OK”

![image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*XzlnW_sVtRflEd_je697Iw.png){:.iod}

What’s the Impact? Due to some reason *(Well I think it happened because the application’s module which was responsible to create subdomains had over privileges)* when a user tries to visit login.application.com or tries to recreate his/her session the application gets confused “where to take the user” making the subdomain not reachable ultimately making the application not reachable.

I hope it gave you a clear idea how efficient bug bounty and penetration testing becomes when you use your superpowers rather than relaying on the automation and third party tools.
I hope you’ve learnt something new with this.<br/>
Thank You!
---
{: data-content="footnotes"}

[^1]: [Previous Article]({% post_url 2022-10-13-code-flaws-part-1 %}){:target="_blank"}
[^2]: [Burp Suite](https://portswigger.net/burp){:target="_blank"}