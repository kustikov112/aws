# Task 1 (Cloud Introduction)

## Application Overview

---

Throughout this course you will build a **MapPoints** application — an interactive map where users can:

1. Browse and view map points on a world map.
2. Create new map points with a title, description, and coordinates.
3. Upload photos to map points.
4. Import multiple points at once from a CSV file.
5. Access protected operations only when authenticated and authorized.
6. See photos automatically enriched with AI-detected labels (bonus module).

Each module adds one layer of AWS capability to this same application. By the end of the course you will have a fully serverless, authenticated, media-enriched application deployed with AWS CDK.

## Module-Safe App Setup (Required)

To avoid frontend runtime errors while backend features are still missing, prepare feature flags from the beginning.

Use [feature-flags.template.json](../feature-flags.template.json) and check off:

- [ ] Copy template into frontend project (for example `src/config/feature-flags.json`)
- [ ] Build unfinished features in disabled state
- [ ] Show disabled controls with a short label (for example: "Available in next modules")

You will update these checkboxes module by module in tasks 2-10.

## Tasks

---

### Task 1.1

1. Join our communication channel

- Discord: register a new account if you don't have one and [join the course chat](https://discord.gg/uWvFU2RAba)
- Optionally, get the role in the #role-assigner channel

2. Register in [RS App](https://app.rs.school/registry/student?course=aws-developer-2026q2)

    - The link might be outdated, so be sure to reach out if you have any doubts
    - Feel free to ask any questions in our Discord chat

### Task 1.2

1. Revise:

    - What is REST; what are the main principles of it.
    - How to create a basic REST API service using Express.

2. Read about (if you would like to learn beyond the curriculum):

    - On-Premise architecture
    - Cloud Architecture
    - Cloud benefits
    - What do the region, availability zone, IAM mean?
    - How to use cloud solutions (Read about possible showcases)?
    - What is a serverless architecture?

### Task 1.3

1. Check out the FE app that will be used throughout the course:

    - [MapPoints React App](https://github.com/rolling-scopes-school/gis-app-aws)

2. Clone the FE App repo, install dependencies, run the app and check if everything is okay.

    - Check `package.json` and/or `README.md` for additional details.

3. Familiarize yourself with the target application: a map where users drop pins (points) at geographic coordinates, attach a description, and optionally upload a photo for each point.
4. Verify that the starter frontend works locally before any AWS deployment:

    - `cd starter_app_templates/frontend`
    - `npm install`
    - `npm run build`

    At this stage, no API Gateway URL is required.

### Task 1.4

1. Register in AWS following best practices.

    - Be careful with your root credentials and make sure to protect them properly.

2. Create an IAM user and assign the `AdministratorAccess` policy to it.
3. Using the CLI, connect to your AWS account and get the created IAM user information.

    - Remember to set up AWS credentials on your local machine.
    - Command that needs to work from your terminal: `aws iam get-user --user-name=MyUser`
    - If your environment uses AWS IAM Identity Center / SSO and the session expires, refresh it with `aws login` before trying to deploy any module.

4. Wait for the next task to be announced and help others in the chat if they have any issues.
5. Even though you will likely use a Free Tier account, it is good practice to review approximate resource pricing: [AWS Pricing](https://aws.amazon.com/pricing/)
