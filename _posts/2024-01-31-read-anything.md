---
layout: post
category: posts
title: "Defying Paywalls in the Pursuit of Knowledge, Just as Dad Envisioned"
---
### Index
- [Introduction](#introduction)
- [User Interface](#user-interface)
- [Under The Hood](#under-the-hood)

### [Introduction](#introduction)

In my pursuit of knowledge within a digital audiobook and PDF service, I uncovered a noteworthy security vulnerability that facilitated unrestricted access to all content, including unpublished manuscripts, without the need for a subscription. <br/>
This exploration stemmed from the belief instilled by my father, emphasizing the inherent freedom of knowledge in contrast to the prevailing model that demanded substantial financial investments for each literary endeavor. 

### [User Interface](#user-interface)
As a regular user navigating the platform, my initial attempts to access content without a subscription through the user interface proved unfruitful.<br/>
Serendipitously, I stumbled upon the authors' subdomain—a space dedicated to writing and publishing. `https://community.censored.com`<br/><br/>
However, despite this discovery, the conventional interface lacked a straightforward method to access the literary works of others without financial obligations, indicating a critical gap in user accessibility.

### [Under The Hood](#under-the-hood)
Transitioning into a more technical exploration, I meticulously scrutinized the functionality of the authors' subdomain. Through the monitoring of network requests, a fascinating revelation emerged: the submission of a book for review triggered a browser request to open the submitted book.<br/>
```
GET /api/v1/spark/novels/{Integer_IDs}/
Host: community.censored.com
```
Seizing this opportunity, I exploited the system by modifying the numerical book IDs in the requests. Given that the book IDs were numerical, I successfully engaged in a brute force attack, systematically testing and accessing different IDs. This not only granted unauthorized access to my own submissions but also to the works of fellow authors, spotlighting a critical security vulnerability in the nuanced interplay between the author's submission process and the underlying review infrastructure.