---
title: "Step-by-step Lab Guide"
date: 2026-11-11
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

### Step 1. Run locally (optional check)
```sh
cd aurel-restaurant
pnpm install --frozen-lockfile
pnpm setup
pnpm dev
```
Open http://127.0.0.1:3000. `pnpm setup` creates `.env.local` with a random password and session key. Do not commit this file.

### Step 2. Deploy the AWS backend
```sh
aws configure sso
aws sso login --profile YOUR_PROFILE
export AWS_PROFILE=YOUR_PROFILE
export AWS_REGION=ap-southeast-1
pnpm build:lambda
sam validate --lint --template-file infra/template.yaml
sam build --template-file infra/template.yaml
sam deploy --guided
```
Guided answers for the first deployment:
- Stack name: `aurel-backend`; Region: `ap-southeast-1`.
- `FrontendOrigin`: `https://pending.invalid` for now (updated in Step 7).
- `SenderEmail`: a real email that you own. Open the verification email from AWS and confirm it while the stack is being created.
- Allow SAM to create IAM roles.

Read the stack outputs (`ApiUrl`, `TableName`, `MediaBucket`, `UserPoolId`, `CognitoClientId`):
```sh
aws cloudformation describe-stacks --stack-name aurel-backend \
  --query 'Stacks[0].Outputs' --output table
```

### Step 3. Seed the menu
```sh
export TABLE_NAME=YOUR_TABLE_OUTPUT
node dist/seed-aws.mjs
```
The script only adds IDs that do not exist yet and can be run again.

### Step 4. Create the admin user in Cognito
```sh
export USER_POOL_ID=YOUR_POOL_OUTPUT
export ADMIN_EMAIL=YOUR_REAL_EMAIL
node scripts/create-admin.mjs
```
The password must have at least 14 characters with upper case, lower case, digits and symbols. The script adds the user to the `admin` group.

### Step 5. Prepare SES
In the SES sandbox, both the sender and the test recipient must be verified. To send to real guests, request SES production access in the same Region.

### Step 6. Deploy the frontend on Amplify Hosting
1. Push the project folder as the root of your GitHub repository (do not push `node_modules`, `.next`, `.env.local`, `.data`, `dist`, `.aws-sam`).
2. In Amplify, choose **Create new app**, connect the repository and branch, and select Node 22 in the build image. The repository already contains `amplify.yml`.
3. Add these environment variables before building (never add AWS access keys):

| Variable | Value |
| --- | --- |
| `DATA_MODE` | `aws` |
| `API_BASE_URL` | `ApiUrl` output |
| `NEXT_PUBLIC_API_BASE_URL` | same `ApiUrl`, without trailing `/` |
| `COGNITO_CLIENT_ID` | `CognitoClientId` output |
| `COGNITO_USER_POOL_ID` | `UserPoolId` output |
| `APP_AWS_REGION` | `ap-southeast-1` |
| `APP_ORIGIN` | exact HTTPS origin of the frontend, without trailing `/` |

The default origin looks like `https://main.APP_ID.amplifyapp.com`. If a build ran before the variables were complete, set them and run the build again.

### Step 7. Update the backend CORS origin
Run `sam deploy --guided` again and replace `FrontendOrigin` with the real Amplify URL. Redeploy the frontend if any variable changed, because `NEXT_PUBLIC_*` values are packaged at build time.
