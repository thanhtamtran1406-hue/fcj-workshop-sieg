---
title: "CloudTrail and GuardDuty: how do you know who did what in an AWS account?"
date: 2026-11-11
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

After learning about IAM, I had a follow-up question: if someone uses my key, how do I know what they did and when? That question led me to AWS CloudTrail and Amazon GuardDuty.

CloudTrail records API calls in the account, whether they come from the console, the CLI, or an SDK. Each event shows who made the call, what was called, from which IP address, at what time, and whether it succeeded. By default, the Event history page lets you view management events from the last 90 days in each Region, and viewing is free of charge. To keep a continuous record beyond 90 days, you need to create a trail that delivers logs to S3. AWS recommends using a multi-Region trail so that no activity is missed. The trade-off is that one copy of management events delivered to S3 is not charged by CloudTrail, but I still pay for S3 storage and am responsible for protecting the bucket that holds the logs.

GuardDuty does a different job. It is a threat detection service that continuously analyzes data sources such as CloudTrail management events, VPC flow logs, and DNS logs, and then produces findings with a severity from 1.0 to 8.9, where High falls between 7.0 and 8.9. The difference is that CloudTrail gives you raw data to investigate on your own, while GuardDuty proactively alerts you to anything unusual. The two services complement rather than replace each other. The first time GuardDuty is enabled in a Region, the account gets a 30-day trial in that Region; after the trial, usage-based charges apply, so I check the pricing page and set a calendar reminder before the trial ends.

I will verify this with an incident-simulation lab, making use of my digital forensics knowledge:

1. Create a lab IAM user and use that user's key to run a few CLI commands, including one that is deliberately denied.
2. Find these events in CloudTrail Event history by user name or event name.
3. Record the time, source IP, and the AccessDenied error code to build a timeline.
4. Generate sample findings in GuardDuty (titles start with [SAMPLE]) to see the structure and severity of an alert.

For operations, I would consider a trail that applies to multiple Regions, block public access to the log bucket, and limit who can delete logs. A logging system that an attacker can delete is no longer trustworthy.

My takeaway: CloudTrail answers the question "what happened", while GuardDuty helps answer "is anything suspicious". How useful the alerts are has to be verified with a lab in my own account.

<img src="/fcj-workshop-sieg/images/3-BlogsPosted/3.3-Blog3/cloudtrail-guardduty.png" alt="Diagram of the CloudTrail and GuardDuty flow" style="max-width:100%; width:700px;">

References:

1. Working with CloudTrail event history: <https://docs.aws.amazon.com/awscloudtrail/latest/userguide/view-cloudtrail-events.html>
2. How CloudTrail works (trail and event history): <https://docs.aws.amazon.com/awscloudtrail/latest/userguide/how-cloudtrail-works.html>
3. What is Amazon GuardDuty?: <https://docs.aws.amazon.com/guardduty/latest/ug/what-is-guardduty.html>
4. Understanding Amazon GuardDuty findings: <https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_findings.html>
5. Generating sample findings in GuardDuty: <https://docs.aws.amazon.com/guardduty/latest/ug/sample_findings.html>
6. Pricing in GuardDuty (30-day free trial): <https://docs.aws.amazon.com/guardduty/latest/ug/guardduty-pricing.html>
