---
title: "Step-by-step lab guide"
date: 2026-11-26
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

### Step 1 — Run and check locally

Run the application on your own machine first, so that the code, the tests and the data model are known to work before anything is created on AWS.

```sh
pnpm install --frozen-lockfile
pnpm setup
pnpm dev
pnpm check && pnpm build && pnpm test:smoke
```
pnpm setup creates a local .env.local; do not commit it.

**Expected result:** pnpm check and pnpm build finish without errors, the smoke suite passes, and the site opens locally with the menu loaded.

### Step 2 — Deploy the AWS backend

The backend is deployed with CloudFormation in two stacks: aurel-artifacts for the package bucket and aurel-backend for the API, database, authentication and media storage.

```sh
export AWS_PROFILE=aurel-lab AWS_REGION=ap-southeast-1
export SENDER_EMAIL='YOUR_OWN_VERIFIED_EMAIL'
pnpm build:lambda
aws cloudformation deploy --stack-name aurel-artifacts --template-file infra/artifacts.yaml
export ARTIFACT_BUCKET=$(aws cloudformation describe-stacks --stack-name aurel-artifacts \
  --query 'Stacks[0].Outputs[?OutputKey==`BucketName`].OutputValue | [0]' --output text)
aws cloudformation package --template-file infra/template.yaml \
  --s3-bucket "$ARTIFACT_BUCKET" --s3-prefix lambda --output-template-file packaged-backend.yaml
aws cloudformation deploy --stack-name aurel-backend --template-file packaged-backend.yaml \
  --capabilities CAPABILITY_IAM CAPABILITY_AUTO_EXPAND \
  --parameter-overrides FrontendOrigin=https://pending.invalid SenderEmail="$SENDER_EMAIL"
aws cloudformation describe-stacks --stack-name aurel-backend --query 'Stacks[0].Outputs' --output table
```
Confirm the verification email from AWS while the stack is being created. Then seed the menu and create the admin user:
```sh
export TABLE_NAME=YOUR_TABLE_OUTPUT
node dist/seed-aws.mjs
python3 bootstrap-admin.py --pool YOUR_USER_POOL_ID --email YOUR_ADMIN_EMAIL
```
The seed script only adds missing dishes and can be run again. The admin script asks for the password interactively, adds the user to the admin group and does not allow self-registration. Quick check:
```sh
curl -i "$API_URL/menu"                 # 200
curl -i "$API_URL/admin/reservations"   # 401
```

**Expected result:** the aurel-backend stack reaches CREATE_COMPLETE and prints its outputs (ApiUrl, table, buckets, user pool). /menu returns 200 with the 100 seeded dishes, and /admin/reservations returns 401 without a token.

### Step 3 — Frontend and CI/CD

```sh
python3 scripts/package-source.py --output ../aurel-codecommit-source.zip
aws s3 cp ../aurel-codecommit-source.zip "s3://$ARTIFACT_BUCKET/source/aurel-lab.zip"
aws cloudformation deploy --stack-name aurel-frontend --template-file infra/amplify-codecommit.yaml \
  --capabilities CAPABILITY_IAM --parameter-overrides SourceBucket="$ARTIFACT_BUCKET" \
  SourceKey=source/aurel-lab.zip ApiUrl="$API_URL" CognitoClientId="$COGNITO_CLIENT_ID" \
  CognitoUserPoolId="$USER_POOL_ID"
```
The template creates a CodeCommit repository, a read-only role and the Amplify app. Use these environment variables (never admin passwords, session secrets or AWS keys):

| Variable | Value |
| --- | --- |
| DATA_MODE | aws |
| API_BASE_URL, NEXT_PUBLIC_API_BASE_URL | ApiUrl, no trailing slash |
| COGNITO_CLIENT_ID, COGNITO_USER_POOL_ID | backend outputs |
| APP_AWS_REGION | ap-southeast-1 |
| APP_ORIGIN | exact frontend origin |

Then set the real frontend origin in the backend and start an Amplify job:
```sh
aws cloudformation deploy --stack-name aurel-backend --template-file packaged-backend.yaml \
  --capabilities CAPABILITY_IAM CAPABILITY_AUTO_EXPAND \
  --parameter-overrides FrontendOrigin="$FRONTEND_ORIGIN" SenderEmail="$SENDER_EMAIL"
aws amplify start-job --app-id "$APP_ID" --branch-name main --job-type RELEASE
aws amplify list-jobs --app-id "$APP_ID" --branch-name main --max-results 5
```
Treat the deployment as successful only when the job status is SUCCEED. The ZIP seeds the repository only once; later updates are Git pushes to main, which Amplify builds automatically.

**Expected result:** the Amplify job ends with status SUCCEED, the website opens at its Amplify address, the menu loads from the API without CORS errors, and an admin can sign in.
