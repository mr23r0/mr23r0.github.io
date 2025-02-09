---
layout: post
category: posts
title: "The Curious Case of Unlimited AI Calls"
description : "The Curious Case of Unlimited AI Calls, is a tale about how I managed to find a vulnerability which allows an attacker to bypass API rate limits and make unlimited AI calls"
tags : "API Security, Rate Limiting, Penetration Testing, Bug Bounty, Ethical Hacking, Security Research, Web Security, OWASP, Server-Side Validation, API Abuse, Cybersecurity, Responsible Disclosure, AI Security"
ai_generated: true
---

## Introduction

Imagine walking into a coffee shop that charges for refills. You get your first cup, but instead of paying for more, you find a secret trick to keep getting unlimited refills—all without the cashier noticing. Sounds fun, right? Well, something similar happened in the digital world with an AI-powered platform that charged users for making AI requests. Due to a simple yet impactful vulnerability, it was possible to override the limit and get unlimited AI requests—for free.

Let's dive into how this issue worked, why it happened, and what security professionals can learn from it.

## The AI That Didn't Know When to Stop

The AI platform in question allowed users to generate content using artificial intelligence. Users on free accounts were given a limited number of AI requests, and once they hit the limit, they had to either wait or upgrade to a paid plan. This limit was meant to ensure fair usage and encourage users to subscribe.

But here's where things got interesting: instead of enforcing these limits strictly on the backend, the system relied on information **sent from the client-side**. This means the application itself was telling the server, "Hey, I've hit my limit!"—something that, as a security researcher, immediately caught my attention.

## How the Bug Worked

This AI service had an API endpoint that handled AI requests:

```
POST /designer-api/api/v1/generation-prompt HTTP/1.1
Host: api.example.com
Authorization: Bearer <user_token>
Content-Type: application/json

{
  "prompt": "Generate an AI response",
  "stats": {
    "remaining_calls": 15,
    "limit": 15
  }
}
```

Each request contained a **stats** section that included details about the user's usage limits. Normally, when a user reached their limit, they would get a **429 Too Many Requests** error:

```
HTTP/1.1 429 Too Many Requests
Content-Type: application/json

{
  "error": "Rate limit exceeded"
}
```

But what if we could simply **change the number** in the request body before it reached the server? That's exactly what was possible here! By modifying the stats section, an attacker could:

- Trick the system into thinking they still had AI requests left
- Continue making API calls well beyond the intended limit
- Effectively get an **unlimited** AI service for free

### Visual Breakdown of the Issue

<div class="mermaid">
sequenceDiagram
    participant User
    participant Client
    participant Server
    User->>Client: Sends AI request
    Client->>Server: {"remaining_calls": 15, "limit": 15}
    Note right of User: Modifies request body
    Client->>Server: {"remaining_calls": 100, "limit": 100}
    Server->>Client: AI response (No rate limit enforcement)
</div>

## Why Did This Happen?

### 1. **Trusting Client-Side Data**
One of the golden rules of security is **never trust user input**—and that includes request data. The AI platform mistakenly relied on a value in the request body to determine rate limits instead of tracking it properly on the server side.

### 2. **Lack of Server-Side Validation**
A well-designed system should verify limits **independently** on the backend. In this case, since the API simply accepted what the request told it, there was no mechanism ensuring that the limit was actually being enforced.

### 3. **Failure to Use Secure Tokens or Signatures**
Many modern APIs use cryptographic signatures (like HMAC) to verify request integrity. Had this platform signed the request metadata, any tampering would have been detected.

## Lessons for Security Professionals

### 🔹 **Enforce Rate Limits Server-Side**
Never rely on client-side values for enforcing restrictions. Always track API usage in a backend database or a secure session store.

### 🔹 **Use Immutable Request Metadata**
Instead of allowing the request body to dictate limits, the system should generate and store limits on the server side, making them read-only for users.

### 🔹 **Monitor Anomalous Activity**
Rate-limiting dashboards and API monitoring tools can detect when a single user is making an unusually high number of requests, preventing abuse.

For an in-depth guide on API security, check out the [OWASP API Security Top 10](https://owasp.org/www-project-api-security/).

## Conclusion

This case highlights a fundamental security lesson: **never trust the client to enforce security rules**. Whether it's API rate limiting, authentication, or access control, these checks should always be handled **on the server**. 

Interestingly, after responsibly disclosing this issue, the company's security team acknowledged the value of the report, stating it was one of the most impactful submissions they had received from an external researcher. As a token of appreciation, they issued a **$800 reward**—a rare gesture that underscores the importance of proactive security research.

Have you encountered a similar vulnerability? Let's discuss in the comments below!
