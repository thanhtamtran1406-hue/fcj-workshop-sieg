---
title: "Overview"
date: 2026-11-11
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

AUREL Dining & Wine is a demo restaurant website built with Next.js 15.5.28, React 19, TypeScript and Tailwind CSS. The restaurant name, address and dish prices are sample data. This workshop deploys it on AWS: the frontend runs on AWS Amplify Hosting, and the backend is built with AWS SAM (API Gateway HTTP API, Lambda Node.js 22, DynamoDB, Cognito, SES and a private S3 bucket).

**Website features**
- Home, Menu (100 dishes read from the API, filtering, search, pagination), Gallery, About, Contact.
- Table reservation: a new reservation is a request waiting for confirmation, not a confirmed table.
- Admin page: sign-in, reservation list with confirm/cancel, menu CRUD, contact messages, image upload to private S3.

**Source code:** <https://github.com/ndthuy21/aurel-restaurant>

AWS resources may incur charges depending on usage; this setup is not guaranteed to stay within the Free Tier. The examples use the `ap-southeast-1` Region.
