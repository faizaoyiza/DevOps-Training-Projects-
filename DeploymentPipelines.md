# Mini Project: Deployment Pipelines and Cloud Platforms with GitHub Actions

## Introduction

In this mini project, I explored how **GitHub Actions** can be used to automate software deployment through Continuous Integration and Continuous Deployment (CI/CD). I learned how deployment pipelines streamline the software delivery process, automate versioning and releases, authenticate securely with cloud providers, and deploy applications automatically to **Amazon Web Services (AWS)** whenever changes are pushed to the GitHub repository.

This project demonstrates how GitHub Actions integrates source control, automated workflows, cloud authentication, and deployment into a complete DevOps pipeline.

---

# Project Objectives

The objectives of this project were to:

- Understand the stages of a deployment pipeline.
- Learn common deployment strategies.
- Automate software versioning and release creation.
- Configure GitHub Secrets for secure cloud authentication.
- Deploy an application automatically to AWS using GitHub Actions.
- Separate deployment environments such as development, staging, and production.

---

# Prerequisites

Before starting this project, the following tools and knowledge were required:

- GitHub account
- Basic Git knowledge
- Familiarity with YAML syntax
- AWS account
- AWS CLI installed
- Node.js and npm installed
- Visual Studio Code or another code editor
- Basic understanding of CI/CD concepts

---

# Understanding Deployment Pipelines

A deployment pipeline is a series of automated stages that move code from development to production.

The stages include:

## Development

Developers write code, fix bugs, and perform local testing.

## Integration

Changes are merged into the shared repository using Git.

## Testing

Automated tests verify that the application functions correctly.

## Staging

The application is deployed to a staging environment that closely resembles production.

## Production

After successful testing, the application is deployed for end users.

---

# Deployment Strategies

During this project, I learned several deployment strategies commonly used in DevOps.

## Blue-Green Deployment

Two identical production environments exist.

- Blue = Current production
- Green = New release

Traffic switches to the new environment after successful validation, minimizing downtime.

## Canary Deployment

The new version is released to a small percentage of users before rolling it out to everyone.

This reduces deployment risks.

## Rolling Deployment

Servers are updated gradually until every instance runs the latest version.

This avoids complete downtime.

---

# Creating a GitHub Actions Workflow

GitHub Actions workflows are stored inside:

```text
.github/workflows/
```

For deployment, I created the workflow:

```text
deploy-to-aws.yml
```

The workflow automatically executes whenever code is pushed to the **main** branch.

```yaml
name: Deploy to AWS

on:
  push:
    branches:
      - main
```

---

# Workflow Structure

The deployment workflow performs the following tasks:

1. Checkout the latest source code.
2. Configure AWS credentials.
3. Build the application.
4. Deploy the application to AWS.
5. Verify successful deployment.

Example:

```yaml
jobs:
  deploy:

    runs-on: ubuntu-latest

    steps:

      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v4

      - name: Build Application
        run: npm install && npm run build

      - name: Deploy to AWS
        run: aws s3 sync ./build s3://my-static-website
```

This workflow automatically deploys the latest application whenever new code is pushed.

---

# Cloud Authentication Using GitHub Secrets

To securely connect GitHub Actions with AWS, I configured GitHub Secrets instead of storing credentials inside the repository.

The following secrets were added:

| Secret | Description |
|---------|-------------|
| AWS_ACCESS_KEY_ID | AWS Access Key |
| AWS_SECRET_ACCESS_KEY | AWS Secret Key |
| AWS_REGION | Deployment Region |

Inside the workflow:

```yaml
- name: Configure AWS Credentials

  uses: aws-actions/configure-aws-credentials@v4

  with:
    aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
    aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
    aws-region: ${{ secrets.AWS_REGION }}
```

Using GitHub Secrets ensures sensitive credentials remain encrypted and secure.

---

# Automating Versioning

To automate releases, Semantic Versioning (SemVer) was implemented.

The project follows:

```text
MAJOR.MINOR.PATCH
```

Examples:

```text
v1.0.0
v1.0.1
v1.0.2
```

Every push to the **main** branch automatically increments the patch version.

Example workflow:

```yaml
name: Version Application

on:
  push:
    branches:
      - main

jobs:

  version:

    runs-on: ubuntu-latest

    steps:

      - uses: actions/checkout@v4

      - name: Bump Version

        run: |
          npm version patch
          git push --follow-tags
```

This automatically creates a new version tag for every successful deployment.

---

# Automating GitHub Releases

After a new version tag is created, GitHub Actions automatically creates a GitHub Release.

Example workflow:

```yaml
name: Create GitHub Release

on:

  push:

    tags:

      - 'v*'
```

Release step:

```yaml
- name: Create Release

  uses: actions/create-release@v1

  env:

    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

  with:

    tag_name: ${{ github.ref_name }}

    release_name: Release ${{ github.ref_name }}
```

Each tagged version is stored as a GitHub Release, making deployments easier to track and download.

---

# Deploying to AWS

For cloud deployment, GitHub Actions automatically uploads the application to Amazon Web Services.

Example deployment command:

```yaml
- name: Deploy to AWS S3

  run: |

    aws s3 sync ./build s3://my-static-website
```

This uploads the application files directly to the configured S3 bucket.

The deployment process is fully automated and requires no manual intervention.

---

# Environment-Specific Deployment

Different Git branches were configured to deploy to different environments.

| Branch | Environment |
|----------|-------------|
| develop | Development |
| staging | Staging |
| main | Production |

Example:

```yaml
on:

  push:

    branches:

      - develop

      - staging

      - main
```

Conditional deployment:

```yaml
- name: Deploy Development

  if: github.ref == 'refs/heads/develop'

  run: aws s3 sync ./build s3://dev-bucket

- name: Deploy Staging

  if: github.ref == 'refs/heads/staging'

  run: aws s3 sync ./build s3://staging-bucket

- name: Deploy Production

  if: github.ref == 'refs/heads/main'

  run: aws s3 sync ./build s3://production-bucket
```

This ensures each branch deploys to its own cloud environment.

---

# Security Best Practices

To improve security throughout the deployment process, I implemented the following best practices:

- Stored AWS credentials using GitHub Secrets.
- Avoided hardcoding passwords and access keys.
- Used least-privilege IAM permissions.
- Restricted deployments to protected branches.
- Reviewed GitHub Actions logs after every deployment.
- Used encrypted authentication for cloud access.

---

# Key Concepts Learned

Through this project, I learned how to:

- Build deployment pipelines using GitHub Actions.
- Automate application deployment to AWS.
- Configure GitHub Secrets for cloud authentication.
- Create reusable workflow files using YAML.
- Implement Semantic Versioning.
- Automatically generate Git tags.
- Automatically create GitHub Releases.
- Deploy different branches to separate cloud environments.
- Improve deployment security using encrypted credentials.

---

# Conclusion

This project provided practical experience in automating cloud deployments using GitHub Actions and AWS. I successfully created deployment workflows that automatically build and deploy applications whenever changes are pushed to GitHub. I also implemented secure cloud authentication using GitHub Secrets, automated Semantic Versioning, generated Git tags, and created GitHub Releases to improve release management.

Additionally, I configured environment-specific deployments so that the **develop**, **staging**, and **main** branches deploy to separate AWS environments. These practices align with modern DevOps principles by improving automation, consistency, security, and deployment reliability while reducing manual intervention.
