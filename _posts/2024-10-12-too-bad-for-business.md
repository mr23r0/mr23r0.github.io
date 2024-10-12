---
layout: post
category: posts
title: "Too Bad for Business - an IDOR Vulnerability"
description : "Too Bad for Business - an IDOR Vulnerability, is a tale about how I managed to find an IDOR vulnerability which risks the company's entire business model"
tags : "idor"
---

Whether you're 15 or 60, you've probably heard of and used an e-signature application at least once. These apps are meant to help share confidential documents for signing, so it’s no surprise that keeping documents safe from hackers is a key part of their business.

But, of course, nothing is ever that easy, if it were, it wouldn’t be life! These apps usually come with certain features to securely share documents, including strict rules on how the documents can be opened and how long they stay accessible. The person managing the application (the Instance Administrator) can adjust these settings based on what they or their company needs.

![E-Signature-Application]({{ site.baseurl }}/assets/img/e-sign-app-struct.png)

Some of the basic features from this set are,
- Document Editing
- Signing Field Mapping
- Sharing Document for Signature
- Document Download (Sender and Receiver, on both ends), and etc.

Implementing these features securely is a challenge on its own, not to mention keeping the same format and quality across all parts of the application.

I recently came across a similar case during one of my testing projects. It was a well-known but mid-sized company/product, with strong security against Insecure Direct Object Reference (IDOR), though I did find a few low-severity bugs.<br/>

I spent a couple of hours reviewing everything, and while I had four low-severity bugs on my list, there weren’t any other clear vulnerabilities to exploit so, I shifted my focus to learning more about the application.<br/>

On their data protection policy page, I found that they store every document on AWS, with encryption at rest and a valid access token required to access the documents. Sounds pretty secure.

![Document-Transaction]({{ site.baseurl }}/assets/img/doc-tran-aws.png){:.iod}

Uploading the document and linking it to the user account seemed secure enough, but let’s be honest—that’s not the fun part to exploit. If I could somehow get access to documents from other accounts... now that would be interesting!<br/>

I set up some documents and started the signing request process. However, the URL provided to the end user for accessing the document fetched it directly from the application server. The application was temporarily storing the document on its own server as a failsafe mechanism.
To understand this better, I recommend watching this [Project Air video](https://www.youtube.com/watch?v=tEpn-6dBn-M&t=330){:target="_blank"}. It explains a similar issue in a really simple way.

If that wasn’t clear, here’s a quick summary: Loading the document from AWS would mean somehow sharing the encryption keys and a valid token with the end user, which is hard to secure. The user could extract those credentials and keep access to the document, including any future updates. Or the application would need to constantly regenerate and update those credentials everywhere. 

To avoid this hassle, the application temporarily stores the document on its server, and once the signing is done, it updates the document on AWS. Even the download button on the user end just created a PDF of the loaded document and exported it. It seemed too good to be true, everything has vulnerabilities, right? So, I decided to test the same feature from the customer’s end.

When opening the document initially, the application did use AWS directly, and after editing, it uploaded the updated document back to AWS. The credentials used to fetch the document from AWS were stored under the `/organization/users/<ID>/documents` object, and the request wasn’t vulnerable to IDOR.

I dug deeper and found the download document button. When clicked, it opened a new tab with a PDF viewer, where the user is supposed to download the document. The most interesting part, though, is the underlying API requests.
When the download button is pressed, the application sends a request to the server to get the credentials for the document at `/documents/<numerical ID>/creds`. And you guessed it, it was indeed vulnerable to IDOR.

Upon requesting a valid documentID, the application responsed with : 
```
{
    "url":"AWS document url",
    "fields":{
        "key":"document key",
        "contentType":"application/pdf",
        "xawzCredential":"credential",
        "xawzAlgorythm":"some value",
        "xawzDate":"timestamp",
        "xaszSignature":"value"
    }
}
```

I gained access to all the documents ever uploaded to the e-signature application, whether they were private or public, shared for signing or not.

I hope you found some insights in this post. If you did or if you have suggestions to help me improve my writing, feel free to send me feedback using the button at the bottom of the page.

Until next time,
Cheers!