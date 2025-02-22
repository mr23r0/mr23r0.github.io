---
layout: post
category: resources
title: "Getting Started with Cloud Security"
description : ""
tags : ""
---
### Index
- [Introduction](#introduction)
- [Fundamentals](#fundamentals)
- [Security Basics](#security-basics)
- [Compliance](#compliance)
- [Conclusion](#conclusion)

### [Introduction](#introduction)
Cloud security isn’t something you can just point to and say, “I know this and that, so I know cloud security.” Like the entire security domain, it consists of multiple components that form its foundation. In this post, I'll do my best to provide a starting point to help you navigate this vast field.


### [Fundamentals](#fundamentals)
Before diving into cloud security, as the name suggests, you first need a solid understanding of the cloud itself. But even before that, I believe you should be familiar with these key concepts:
- [IaaS](https://www.ibm.com/think/topics/iaas){:target="_blank"}, or infrastructure as a service, is on-demand access to cloud-hosted physical and virtual servers, storage and networking—the backend IT infrastructure for running applications and workloads in the cloud.

- [PaaS](https://www.ibm.com/think/topics/paas){:target="_blank"}, or platform as a service, is on-demand access to a complete, ready-to-use, cloud-hosted platform for developing, running, maintaining and managing applications.

- [SaaS](https://www.ibm.com/think/topics/saas){:target="_blank"}, or software as a service, is on-demand access to ready-to-use, cloud-hosted application software.

Use the embedded links to explore these topics further, IBM provides some of the best videos and articles on the subject. Once you're comfortable with these concepts, we can move on to understanding the cloud itself.
There are multiple cloud platforms in the market but based on the market share they have, we'll priortize the learning
<canvas id="marketShareChart" width="600" height="400"></canvas>
<script>
        const ctx = document.getElementById('marketShareChart').getContext('2d');
        const marketShareChart = new Chart(ctx, {
            type: 'line',
            data: {
                labels: ['AWS', 'Microsoft Azure', 'Google Cloud', 'Alibaba Cloud', 'Oracle', 'Others'],
                datasets: [{
                    label: 'Market Share (%)',
                    data: [30, 21, 12, 4, 3, 30],
                    borderColor: 'rgba(75, 192, 192, 1)',
                    backgroundColor: 'rgba(75, 192, 192, 0.2)',
                    fill: true,
                    tension: 0.1
                }]
            },
            options: {
                scales: {
                    y: {
                        beginAtZero: true,
                        max: 40,
                        title: {
                            display: true,
                            text: 'Market Share (%)'
                        }
                    }
                },
                plugins: {
                    title: {
                        display: true,
                        text: 'Cloud Providers Market Share in Q4 2024'
                    }
                }
            }
        });
    </script>

Looking at market share, it’s smart to focus on AWS, Azure, and Google Cloud (GCP). Some might say that's too much for beginners, but who gives a shit? You’re not going to stay a beginner forever. Better to start now and not get overwhelmed. 😉
- [AWS](https://youtu.be/bgPuPSPZe2U?si=pzvz4ky2U6MvOISr){:target="_blank"} : [Architecture](https://www.youtube.com/watch?v=QbipcgIdSJc){:target="_blank"} : [Deep-Dive](https://www.youtube.com/watch?v=c3Cn4xYfxJY){:target="_blank"}
- [Azure](https://www.youtube.com/watch?v=3Arj5zlUPG4){:target="_blank"} : [Deep-Dive](https://www.youtube.com/watch?v=i6NzKvGUsBs){:target="_blank"}
- Google Cloud, or GCP : [Architecture](https://www.youtube.com/watch?v=uuotOQjOeb0&list=PLwi6PfxEP7zYLVCK_ywVD-RJNDu6mYXbi){:target="_blank"} : [Deep-Dive](https://www.youtube.com/watch?v=jpno8FSqpc8){:target="_blank"}


Next, you’ll need to understand how Identity and Access Management (IAM) works. Within IAM, you’ll come across Role-Based Access Control (RBAC) and [Zero Trust](https://learn.microsoft.com/en-us/security/zero-trust/zero-trust-overview){:target="_blank"} both essential concepts in cloud security. These topics are covered in the deep-dive of each cloud platform, but if you skipped those, here are some great resources to catch up, [here is a one you can try](https://www.anyshift.io/blog/a-deep-dive-in-aws-resources-best-practices-to-adopt-identity-and-access-management-(iam)){:target="_blank"} or [try this](https://www.techtarget.com/searchsecurity/definition/identity-access-management-IAM-system){:target="_blank"}, IAM principles remain fairly similar across all cloud platforms, so once you get the hang of it, transitioning between providers becomes easier.

To wrap up this section, the last key concept is Deployment Models, which you can explore [here](https://spacelift.io/blog/cloud-deployment-models){:target="_blank"}.

### [Security Basics](#security-basics)

In the cloud, there are principles and models, a lot of them. But to get started, you only need to focus on a couple of key ones:
- [Shared Responsibility Model](https://www.wiz.io/academy/shared-responsibility-model){:target="_blank"}
- [CIA Triad : Confidentiality, Integrity and Availability](https://www.cyera.io/blog/the-fundamentals-of-data-security-in-cloud-computing){:target="_blank"} (this includes more interesting stuff as well, just a little format stuff)

Data security in the cloud ensures that your data is protected from unauthorized access, breaches, and leaks. Two major concepts in data security are encryption and key management. However, when it comes to the cloud, things get more granular.

Each cloud platform provides its own key management service to handle encryption keys securely, such as [AWS KMS](https://www.youtube.com/watch?v=f3APF1dP8w0), [Azure Key Vault](https://www.youtube.com/watch?v=0cwcT06YwPE){:target="_blank"} and [Google Cloud KMS](https://www.youtube.com/playlist?list=PLi133fNSP6SiMDGwngZGaxFQYfWHMDAOW){:target="_blank"}.

These services ensure that encryption keys are managed securely and efficiently, reducing the risk of unauthorized access to sensitive data.

Like any other aspect of security, nothing is complete without logs, monitoring, and security systems, and the cloud is no exception. Each cloud platform has its own logging and monitoring services. Some key examples include : 
- [CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html){:target="_blank"}
- [Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/what-is-securityhub.html){:target="_blank"}
- [GuardDuty](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/guard-duty-rds-protection.html){:target="_blank"}
- [SIEM](https://www.ibm.com/think/topics/siem){:target="_blank"}
- [SCC](https://cloud.google.com/security/products/security-command-center){:target="_blank"}
- [Defender for Cloud](https://azure.microsoft.com/en-us/products/defender-for-cloud){:target="_blank"}
- [CSPM](https://www.wiz.io/academy/what-is-cloud-security-posture-management-cspm){:target="_blank"}
- [Secrets Management](https://www.cyberark.com/what-is/secrets-management/){:target="_blank"}
- [Code Signing](https://www.encryptionconsulting.com/education-center/what-is-code-signing/){:target="_blank"}
- [SAST/DAST](https://circleci.com/blog/sast-vs-dast-when-to-use-them/){:target="_blank"}

### [Compliance](#compliance)
Compliance plays a major role in any infrastructure. It helps build customer trust by ensuring that the vendor follows a predefined set of security guidelines to protect data. These standards are updated periodically, so it's always a good idea to do a quick Google search to find the latest official and free documentation for learning.

Some key compliance frameworks include:

- SOC 2
- HIPAA
- ISO 27001
- NIST
- CIS Benchmarks

Organizations use Cloud Security Posture Management (CSPM) tools to maintain compliance and ensure configurations align with security best practices. However, compliance alone isn't enough, concepts like Zero Trust, Least Privilege, and other security best practices also play a crucial role in keeping cloud environments secure.

### [Conclusion](#conclusion)
As you've seen, many sub-services are involved in keeping infrastructure and applications running in the cloud. The only way to identify vulnerabilities is by following best practices, particularly in areas like misconfigurations and exposed data.

Like everything else in security, cloud security is an ever-evolving field. Every month, new services emerge, along with new ways to misconfigure them, making entire systems vulnerable. This means continuous learning is essential.

To stay ahead, it's highly recommended to deep dive into specific cloud platforms, ensuring a strong understanding of security configurations and their behaviors within each platform.