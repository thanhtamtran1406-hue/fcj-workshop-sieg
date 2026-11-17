---
title: "Week 7 Worklog"
date: 2026-11-17
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Week 7 Objectives:

* Run the AUREL project and deploy the backend with AWS SAM

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Run the AUREL project locally<br>&emsp;+ pnpm install --frozen-lockfile<br>&emsp;+ pnpm setup<br>&emsp;+ pnpm dev | 19/10/2026 | 19/10/2026 | [https://github.com/ndthuy21/aurel-restaurant](https://github.com/ndthuy21/aurel-restaurant) |
| 3 | - Study the backend components<br>&emsp;+ API Gateway HTTP API<br>&emsp;+ Lambda Node.js 22<br>&emsp;+ DynamoDB and Cognito | 20/10/2026 | 20/10/2026 | [https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api.html](https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api.html)<br>[https://docs.aws.amazon.com/lambda/latest/dg/welcome.html](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html)<br>[https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html)<br>[https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html](https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html) |
| 4 | - Build the backend<br>&emsp;+ pnpm build:lambda<br>&emsp;+ sam validate --lint and sam build | 21/10/2026 | 21/10/2026 | [https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html) |
| 5 | - Deploy the backend with sam deploy --guided<br>&emsp;+ Stack aurel-backend, Region ap-southeast-1<br>&emsp;+ Read the stack outputs | 22/10/2026 | 22/10/2026 | [https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html) |
| 6 | - Seed the menu data into DynamoDB (node dist/seed-aws.mjs)<br>- Create the admin user in Cognito and add it to the admin group (scripts/create-admin.mjs) | 23/10/2026 | 23/10/2026 | [https://github.com/ndthuy21/aurel-restaurant](https://github.com/ndthuy21/aurel-restaurant)<br>[https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html)<br>[https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html](https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html) |

### Week 7 Achievements:

* The project runs locally
* The backend runs on AWS, with menu data and an admin account
