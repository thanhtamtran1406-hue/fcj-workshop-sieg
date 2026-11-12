---
title: "Clean-up"
date: 2026-11-11
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

1. Delete the Amplify app when it is no longer used.
2. Delete the backend stack:
```sh
sam delete --stack-name aurel-backend
```
3. DynamoDB and S3 use the Retain policy, so they remain after the stack is deleted and can still be charged. After backing up and confirming that the data is not needed, delete the table and the bucket manually (empty the bucket first).
4. The log group, SES identity and Cognito resources in the stack are not retained. Check the CloudFormation deletion events to see which resources are left.
