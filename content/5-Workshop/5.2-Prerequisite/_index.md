---
title: "Prerequisites"
date: 2026-11-26
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

**Accounts and access**
- A dedicated AWS lab account in `ap-southeast-1`. Do not reuse stack names from another project.
- Permissions for CloudFormation, IAM, DynamoDB, S3, Cognito, SES, Lambda, API Gateway, Amplify, CodeCommit and CloudWatch.
- An email address that you own, used as `SenderEmail` and verified in SES.

**Tools**
- Node.js 22.13 or later and pnpm 11.25.0 (Corepack).
- AWS CLI v2 and Python 3 (helper scripts for the admin user and monitoring).
- Git. On Windows, run the shell commands in WSL or Git Bash, or convert the variable syntax to PowerShell.

**Rules:** never put AWS keys or passwords in the source code, and use your own resource IDs instead of the demo ones.
