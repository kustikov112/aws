# Cleaning Up Resources After Completing the Course

After finishing the course, remove all AWS resources you created to avoid unexpected charges.

## Table of Contents
1. [Destroy CDK Stacks](#destroy-cdk-stacks)
2. [Remove Cognito User Pool](#remove-cognito-user-pool)
3. [Remove DynamoDB Tables](#remove-dynamodb-tables)
4. [Clean and Delete S3 Buckets](#clean-and-delete-s3-buckets)
5. [Ensure CloudFormation is Clean](#ensure-cloudformation-is-clean)
6. [Remove CloudWatch Log Groups](#remove-cloudwatch-log-groups)
7. [Check Remaining Services](#check-remaining-services)

## Destroy CDK Stacks

The safest way to remove all infrastructure is to destroy the CDK stacks.

1. Navigate to each CDK project directory (e.g. `point_service`, `import_service`, `authorization_service`).
2. Run:
    ```sh
    cdk destroy --all
    ```
3. Confirm the destruction when prompted.

Repeat for each CDK project in your backend repository.

## Remove Cognito User Pool

CDK destroy should handle this, but verify manually:

1. Open the [Amazon Cognito Console](https://console.aws.amazon.com/cognito/).
2. Go to **User Pools**.
3. Select any remaining User Pools and delete them.
4. Go to **Identity Pools** and delete any that exist.

## Remove DynamoDB Tables

CDK destroy should handle the tables created by this course. To verify:

1. Open the [Amazon DynamoDB Console](https://console.aws.amazon.com/dynamodb/).
2. Go to **Tables**.
3. Look for `points`, `point_metadata`, `users`, and `photo_enrichment` tables.
4. Delete any that remain.

## Clean and Delete S3 Buckets

1. Open the [Amazon S3 Console](https://console.aws.amazon.com/s3/).
2. For each bucket created during the course (frontend bucket, imports bucket):
   - Select the bucket.
   - Empty it (delete all objects and folders).
   - Then delete the bucket.

## Ensure CloudFormation is Clean

1. Open the [AWS CloudFormation Console](https://console.aws.amazon.com/cloudformation/).
2. Review the **Stacks** list.
3. Delete any stacks that are no longer needed.

## Remove CloudWatch Log Groups

Lambda functions automatically create log groups. Remove them to avoid minor storage costs:

1. Open the [Amazon CloudWatch Console](https://console.aws.amazon.com/cloudwatch/).
2. Go to **Log groups**.
3. Select all log groups from the course (typically prefixed with `/aws/lambda/`).
4. Choose **Actions → Delete log group(s)** and confirm.

## Check Remaining Services

Verify each service console to make sure nothing was missed:

1. **Lambda** — [Console](https://console.aws.amazon.com/lambda/): ensure no functions remain.
2. **API Gateway** — [Console](https://console.aws.amazon.com/apigateway/): ensure no APIs remain.
3. **SNS** — [Console](https://console.aws.amazon.com/sns/): delete any leftover topics and subscriptions.
4. **SQS** — [Console](https://console.aws.amazon.com/sqs/): delete any leftover queues.
5. **CloudFront** — [Console](https://console.aws.amazon.com/cloudfront/): disable and delete any distributions.
6. **Rekognition** — no persistent resources; no cleanup needed (pay-per-call service).
7. **IAM** — review and remove any roles or policies created specifically for this course.

> **Note:** EC2, RDS, Elastic Beanstalk, and Docker/container resources are **not** used in this course and do not need to be checked.
