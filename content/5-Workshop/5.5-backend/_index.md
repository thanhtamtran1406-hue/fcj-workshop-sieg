---
title: "Step 2 — Deploy the AWS backend"
date: 2026-11-26
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

The backend is deployed with CloudFormation in two stacks: `aurel-artifacts` for the package bucket and `aurel-backend` for the API, database, authentication and media storage.

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
The seed script only adds missing dishes and can be run again. The admin script asks for the password interactively, adds the user to the `admin` group and does not allow self-registration. Quick check:
```sh
curl -i "$API_URL/menu"                 # 200
curl -i "$API_URL/admin/reservations"   # 401
```

**Expected result:** the `aurel-backend` stack reaches `CREATE_COMPLETE` and prints its outputs (`ApiUrl`, table, buckets, user pool). `/menu` returns 200 with the 100 seeded dishes, and `/admin/reservations` returns 401 without a token.
