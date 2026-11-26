---
title: "Step 3 — Frontend and CI/CD"
date: 2026-11-26
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

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
| `DATA_MODE` | `aws` |
| `API_BASE_URL`, `NEXT_PUBLIC_API_BASE_URL` | `ApiUrl`, no trailing slash |
| `COGNITO_CLIENT_ID`, `COGNITO_USER_POOL_ID` | backend outputs |
| `APP_AWS_REGION` | `ap-southeast-1` |
| `APP_ORIGIN` | exact frontend origin |

Then set the real frontend origin in the backend and start an Amplify job:
```sh
aws cloudformation deploy --stack-name aurel-backend --template-file packaged-backend.yaml \
  --capabilities CAPABILITY_IAM CAPABILITY_AUTO_EXPAND \
  --parameter-overrides FrontendOrigin="$FRONTEND_ORIGIN" SenderEmail="$SENDER_EMAIL"
aws amplify start-job --app-id "$APP_ID" --branch-name main --job-type RELEASE
aws amplify list-jobs --app-id "$APP_ID" --branch-name main --max-results 5
```
Treat the deployment as successful only when the job status is `SUCCEED`. The ZIP seeds the repository only once; later updates are Git pushes to `main`, which Amplify builds automatically.

**Expected result:** the Amplify job ends with status `SUCCEED`, the website opens at its Amplify address, the menu loads from the API without CORS errors, and an admin can sign in.
