# Static Frontend Deployment Using GitHub Actions & AWS OIDC

This repository contains the frontend application deployed as a static website on AWS using **Amazon S3 and Amazon CloudFront**.

The deployment process is automated using **GitHub Actions**, with secure AWS authentication through **GitHub OpenID Connect (OIDC)**.

The pipeline retrieves infrastructure details from Terraform remote state and automatically deploys frontend files to the S3 bucket.

---

## Architecture

```text
Developer
    |
    v
GitHub Repository
(static-frontend-github-actions)
    |
    v
GitHub Actions Workflow
    |
    v
GitHub OIDC Authentication
    |
    v
AWS IAM Role
(Frontend Deployment Role)
    |
    v
Retrieve Terraform State from S3
    |
    v
Read Infrastructure Outputs
    |
    v
Upload Frontend Files to Amazon S3
    |
    v
CloudFront Cache Invalidation
    |
    v
Live Static Website
```

---

## AWS Services Used

- **Amazon S3** – Stores frontend static files.
- **Amazon CloudFront** – Provides content delivery and caching.
- **AWS IAM** – Manages deployment permissions.
- **AWS OIDC** – Enables secure GitHub Actions authentication without storing long-term AWS access keys.
- **Amazon Route 53** – DNS management, if configured in the infrastructure.
- **AWS Certificate Manager (ACM)** – SSL/TLS certificate management, if configured in the infrastructure.

---

## Prerequisites

Before deploying this frontend application, the AWS infrastructure must already exist.

The infrastructure should provide:

- S3 bucket for frontend hosting
- CloudFront distribution
- IAM role for GitHub Actions
- Terraform remote state stored in Amazon S3

The frontend deployment workflow reads the infrastructure outputs from the Terraform state file.

---

## GitHub Repository Configuration

Navigate to:

```text
Repository
  → Settings
  → Secrets and variables
  → Actions
```

### 1. GitHub Environment

Create or use the following GitHub Environment:

```text
Development
```

### 2. Repository Secret

Configure the following secret:

| Secret | Description |
|---|---|
| `AWS_ROLE_ARN` | IAM role ARN used for GitHub OIDC authentication |

Example:

```text
arn:aws:iam::716663810213:role/aws-terraform-github-actions-frontend-role
```

### 3. Repository Variable

Configure the following variable:

| Variable | Example |
|---|---|
| `AWS_REGION` | `ap-south-1` |

---

## Terraform Remote State

The frontend workflow retrieves infrastructure outputs from the Terraform state stored in Amazon S3.

**State bucket:**

```text
terraform-backend-bucket-vishal
```

**State file path:**

```text
frontend/webapp/dev/terraform.tfstate
```

The workflow reads these Terraform outputs:

| Terraform Output | Purpose |
|---|---|
| `bucket_name` | Target S3 bucket |
| `cloudfront_distribution_id` | CloudFront cache invalidation |
| `frontend_url` | Deployed website URL |

---

## GitHub Actions Deployment Workflow

The workflow performs the following operations:

1. Checks out the frontend repository.
2. Retrieves GitHub OIDC claims for debugging.
3. Authenticates with AWS using the frontend IAM role.
4. Verifies the AWS identity.
5. Downloads Terraform state from Amazon S3.
6. Reads the required infrastructure outputs.
7. Uploads frontend files to the S3 bucket.
8. Creates a CloudFront invalidation.

The workflow is configured to run on:

```yaml
on:
  push:
    branches:
      - dev
  workflow_dispatch:
```

The GitHub Environment used is:

```yaml
environment:
  name: Development
```

---

## Deployment Steps

### Step 1: Clone the Repository

```bash
git clone https://github.com/VishalKonde01/static-frontend-github-actions.git
```

### Step 2: Configure GitHub Secrets and Variables

Configure:

- `AWS_ROLE_ARN` under the Development environment.
- `AWS_REGION` as a repository variable.

### Step 3: Push Changes to the Dev Branch

```bash
git checkout dev

git add .

git commit -m "Update frontend application"

git push origin dev
```

### Step 4: GitHub Actions Execution

After pushing changes to the `dev` branch, GitHub Actions automatically:

- Starts the deployment workflow.
- Authenticates with AWS using OIDC.
- Retrieves Terraform outputs.
- Syncs frontend files to Amazon S3.
- Invalidates the CloudFront cache.

You can monitor the execution under:

```text
GitHub Repository
  → Actions
  → Frontend CI/CD (Development)
```

### Step 5: Access the Deployed Website

After successful deployment, access the frontend using the `frontend_url` Terraform output.

---

## Repository Structure

```text
static-frontend-github-actions/
│
├── .github/
│   └── workflows/
│       └── frontend-dev.yml
│
├── frontend/
│   ├── index.html
│   ├── app.js
│   ├── style.css
│   └── config.js
│
├── .gitignore
└── README.md
```

---

## Technology Stack

| Category | Technologies |
|---|---|
| Frontend | HTML, CSS, JavaScript |
| CI/CD | GitHub Actions |
| Cloud | AWS |
| Storage | Amazon S3 |
| CDN | Amazon CloudFront |
| Authentication | GitHub OIDC |
| Infrastructure | Terraform |
| Version Control | Git & GitHub |

---

## Key Learnings

- Implemented automated frontend deployment using GitHub Actions.
- Configured secure AWS authentication using GitHub OIDC.
- Used IAM roles instead of long-term AWS access keys.
- Retrieved infrastructure outputs from Terraform remote state.
- Automated S3 synchronization and CloudFront cache invalidation.
- Connected infrastructure provisioning with application deployment.

---

**Project Focus:** Automating static frontend deployment on AWS using GitHub Actions, Terraform remote state, and secure OIDC authentication.