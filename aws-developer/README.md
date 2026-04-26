# MapPoints — AWS Developer Course

Build a real-world serverless application on AWS, module by module.

🗺️ **The Application:** Throughout this course you build **MapPoints** — an interactive map where users browse geographic points of interest, create new points, upload photos, import points in bulk from CSV, authenticate via Cognito, and see AI-generated labels on uploaded photos (bonus module).

 **Comprehensive Journey:** A step-by-step path toward becoming an AWS Certified Developer — Associate.

 **10 Hands-on Practice Tasks:** Each module introduces one AWS service and layers it into the same application.

 **Certification Ready:** By the end of the course you will be well-prepared for the [AWS Certified Developer — Associate](https://aws.amazon.com/certification/certified-developer-associate/) exam.

## What You Should Know Before Starting

You should be comfortable with **Node.js** and have a basic understanding of web development concepts (HTML, CSS, REST APIs).

English level: Intermediate (B1) and up.
Time commitment: at least 10 hours per week.

## Prerequisites

| Tool | Version | Install |
|------|---------|---------|
| Node.js | 20+ | https://nodejs.org |
| AWS CLI v2 | latest | https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html |
| AWS CDK | 2.x | `npm install -g aws-cdk` |
| AWS account | free tier | https://aws.amazon.com/free |

Configure your AWS credentials before starting any CDK task:

```bash
# For IAM user credentials:
aws configure

# For SSO / Identity Center login:
aws login
```

Verify credentials before every deployment:

```bash
aws sts get-caller-identity
```

## Application Architecture

The course architecture is described in [Architecture.pdf](Architecture.pdf).

## Repository Structure

```
gis-app-aws/
├── 01_cloud_introduction/          ← Task description + README per module
├── 02_serving_spa/
├── 03_serverless_api/
├── 04_integration_with_nosql_database/
├── 05_integration_with_s3/
├── 06_async_microservices_communication/
├── 07_authorization/
├── 08_user_management_with_cognito/
├── 09_frontend_authentication/
├── 10_ai_media_enrichment/
├── starter_app_templates/
│   ├── frontend/                   ← Complete React SPA starter (Vite + TypeScript)
│   ├── backend_node/               ← Node.js Lambda handler stubs (implement these)
│   │   ├── point_service/handlers/
│   │   ├── import_service/handlers/
│   │   └── authorization_service/handlers/
│   ├── module_02_infra_node/       ← CDK starter for Task 2 (S3 + CloudFront)
│   ├── FeatureGuard.tsx            ← React helper for flag-gated UI
│   ├── FeatureFlagsPanel.tsx       ← Dev-only flags panel component
│   └── featureFlags.ts             ← Flag reading utility
├── feature-flags.template.json     ← Base flag config — copy into your frontend
└── Architecture.pdf                ← Full application architecture diagram
```

## Getting Started

### Step 1 — Run the frontend locally

```bash
cd starter_app_templates/frontend
npm install
npm run dev
```

Open http://localhost:5173. The map is visible, all backend-dependent features are disabled.

### Step 2 — Configure feature flags

Copy `feature-flags.template.json` into your frontend as `src/config/featureFlags.json`.

Each module task tells you exactly which flags to enable. **Do not enable a flag until you have implemented that feature** — calling unimplemented API endpoints will cause runtime errors.

### Step 3 — Set environment variables

Create `starter_app_templates/frontend/.env.local` (copy from `.env.example`):

```env
# Module 3+
VITE_POINTS_API_URL=

# Module 5+
VITE_IMPORT_API_URL=

# Module 8-9+
VITE_COGNITO_USER_POOL_ID=
VITE_COGNITO_CLIENT_ID=
VITE_COGNITO_DOMAIN=
VITE_COGNITO_REDIRECT_URI=http://localhost:5173
```

Fill in values as you deploy each module. Leave blank what is not yet deployed.

### Step 4 — Bootstrap CDK (once per AWS account/region)

```bash
ACCOUNT=$(aws sts get-caller-identity --query Account --output text)
REGION=$(aws configure get region)
npx cdk bootstrap aws://$ACCOUNT/$REGION
```

## Module-by-Module Guide

### Module 1 — [Cloud Introduction](01_cloud_introduction/README.md)

**AWS services:** IAM, CloudWatch

No deployment needed. Set up your local environment, read the architecture overview, and prepare the feature-flag template in your frontend project.

---

### Module 2 — [Serving SPA](02_serving_spa/README.md)

**AWS services:** S3, CloudFront

Deploy the React frontend to a CDN with the provided CDK starter.

```bash
# Build the frontend
cd starter_app_templates/frontend
npm install && npm run build

# Deploy S3 + CloudFront
cd ../module_02_infra_node
npm install
npm run cdk:deploy
```

Stack output gives you the CloudFront URL.

Feature flags: `module=2`, `ui.showMap=true`

---

### Module 3 — [Serverless API](03_serverless_api/README.md)

**AWS services:** API Gateway, Lambda

Create your own CDK stack. Implement `getPointsList` and `getPointById` in `backend_node/point_service/handlers/` using mock data (at least 5 points with realistic coordinates).

After deploying, set in `.env.local`:
```env
VITE_POINTS_API_URL=https://<id>.execute-api.<region>.amazonaws.com/prod
```

Feature flags: `module=3`, `api.enablePointsApi=true`, `api.pointsSource=mock`

---

### Module 4 — [NoSQL Database (DynamoDB)](04_integration_with_nosql_database/README.md)

**AWS services:** DynamoDB

Create two DynamoDB tables (`points`, `point_metadata`). Implement `createPoint`. Update `getPointsList` and `getPointById` to read from DynamoDB.

Feature flags: `module=4`, `api.pointsSource=dynamodb`, `ui.enableCreatePoint=true`

---

### Module 5 — [S3 Integration](05_integration_with_s3/README.md)

**AWS services:** S3 presigned URLs, S3 event notifications

Create a new `import-service` CDK stack. Implement `getUploadUrl`, `processUploadedPhoto`, `importPointsFile`, and `importFileParser`.

After deploying, set in `.env.local`:
```env
VITE_IMPORT_API_URL=https://<id>.execute-api.<region>.amazonaws.com/prod
```

Feature flags: `module=5`, `ui.enableUploadPhoto=true`, `ui.enableCsvImport=true`

---

### Module 6 — [Async Microservices (SQS + SNS)](06_async_microservices_communication/README.md)

**AWS services:** SQS, SNS

Create `pointsImportQueue` (SQS) and `pointsImportTopic` (SNS). Implement `catalogBatchProcess`. Update `importFileParser` to send rows to SQS instead of just logging.

Feature flags: `module=6`, `async.enableSqsPipeline=true`, `async.enableSnsNotifications=true`

---

### Module 7 — [Authorization](07_authorization/README.md)

**AWS services:** API Gateway Lambda Authorizer

Create `authorization-service`. Implement `basicAuthorizer`. Protect `GET /import` with the Lambda Authorizer.

The lambda reads env vars in the format `{github_login}=TEST_PASSWORD`. The frontend sends `Authorization: Basic base64(login:TEST_PASSWORD)` from `localStorage`.

```js
// Set in browser console before testing import:
localStorage.setItem("authorization_token", btoa("<your_github_login>:TEST_PASSWORD"));
```

Feature flags: `module=7`, `security.enableBasicAuthForImport=true`

---

### Module 8 — [User Management with Cognito](08_user_management_with_cognito/README.md)

**AWS services:** Cognito User Pool, App Client, Cognito Authorizer

Create a Cognito User Pool and App Client via CDK. Implement `postConfirmation` trigger to persist user profiles to DynamoDB. Protect `POST /points` with a Cognito Authorizer.

Feature flags: `module=8`, `security.requireAuthForCreatePoint=true`

---

### Module 9 — [Frontend Authentication](09_frontend_authentication/README.md)

**AWS services:** Cognito Hosted UI, OAuth 2.0

Integrate Cognito Hosted UI into the React SPA. Handle the OAuth 2.0 Authorization Code flow, store tokens, show user email in the header, add a Logout button.

After deploying module 8 Cognito, set in `.env.local`:
```env
VITE_COGNITO_USER_POOL_ID=eu-central-1_xxxxxxxxx
VITE_COGNITO_CLIENT_ID=xxxxxxxxxxxxxxxxxxxx
VITE_COGNITO_DOMAIN=https://your-prefix.auth.<region>.amazoncognito.com
VITE_COGNITO_REDIRECT_URI=https://<cloudfront-domain>/
```

Feature flags: `module=9`, `security.enableCognito=true`, `ui.enableAuthButtons=true`

---

### Module 10 — [AI Media Enrichment (Bonus)](10_ai_media_enrichment/README.md)

**AWS services:** Amazon Rekognition

Implement `enrichPhoto`. Trigger on S3 photo upload, call `rekognition:DetectLabels` (MaxLabels: 10, MinConfidence: 70%), store results in `photo_enrichment` DynamoDB table, and expose them in `GET /points/{pointId}` as `aiLabels`.

Feature flags: `module=10`, `ai.enableRekognitionLabels=true`, `ui.showAiLabels=true`

---

## Backend Handler Reference

All handler stubs are in `starter_app_templates/backend_node/`. Wire them to API Gateway routes or event sources in your own CDK stacks.

```
backend_node/
├── point_service/handlers/
│   ├── getPointsList.ts          ← GET /points                    (module 3)
│   ├── getPointById.ts           ← GET /points/{pointId}          (module 3)
│   └── createPoint.ts            ← POST /points                   (module 4)
├── import_service/handlers/
│   ├── getUploadUrl.ts           ← GET /upload?pointId=&fileName= (module 5)
│   ├── importPointsFile.ts       ← GET /import?fileName=          (module 5)
│   ├── processUploadedPhoto.ts   ← S3 trigger on uploads/         (module 5)
│   ├── importFileParser.ts       ← S3 trigger on uploaded/        (module 5-6)
│   ├── catalogBatchProcess.ts    ← SQS trigger                    (module 6)
│   └── enrichPhoto.ts            ← S3 trigger on uploads/         (module 10)
└── authorization_service/handlers/
    ├── basicAuthorizer.ts        ← Lambda Authorizer               (module 7)
    └── postConfirmation.ts       ← Cognito trigger                 (module 8)
```

## Feature Flags Reference

| Flag | Enable in module |
|------|-----------------|
| `ui.showMap` | 2 |
| `api.enablePointsApi` | 3 |
| `api.pointsSource=mock` | 3 |
| `api.pointsSource=dynamodb` | 4 |
| `ui.enableCreatePoint` | 4 |
| `ui.enableUploadPhoto` | 5 |
| `ui.enableCsvImport` | 5 |
| `async.enableSqsPipeline` | 6 |
| `async.enableSnsNotifications` | 6 |
| `security.enableBasicAuthForImport` | 7 |
| `security.requireAuthForCreatePoint` | 8 |
| `security.enableCognito` | 9 |
| `ui.enableAuthButtons` | 9 |
| `ai.enableRekognitionLabels` | 10 |
| `ui.showAiLabels` | 10 |

## Useful Commands

```bash
# Run frontend locally
cd starter_app_templates/frontend && npm run dev

# Build frontend for deployment
npm run build

# Deploy CDK stack
npx cdk deploy --require-approval never

# Tear down CDK stack
npx cdk destroy

# Check your AWS identity
aws sts get-caller-identity

# Preview CDK changes before deploy
npx cdk diff
```

## Recommended AWS Learning Resources

- [Developer Learning Plan](https://explore.skillbuilder.aws/learn/learning_plan/view/84/developer-learning-plan) — prepares for AWS Certified Developer — Associate
- [Serverless Learning Plan](https://explore.skillbuilder.aws/learn/learning_plan/view/92/serverless-learning-plan) — serverless design patterns and best practices
- [AWS CDK v2 docs](https://docs.aws.amazon.com/cdk/v2/guide/home.html)
- [AWS SDK v3 for JavaScript](https://docs.aws.amazon.com/sdk-for-javascript/v3/developer-guide/welcome.html)
