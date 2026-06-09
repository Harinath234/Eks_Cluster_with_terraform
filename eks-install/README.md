# Terraform EKS Deployment Workflow

## Architecture Flow

```text
GitHub
   ↓
Jenkins Checkout
   ↓
Terraform Init
   ↓
Read Backend Configuration
   ↓
Connect S3 Bucket + DynamoDB Table
   ↓
Terraform Plan
   ↓
Read VPC Module
   ↓
Read EKS Module
   ↓
Terraform Apply
   ↓
AWS VPC Created
AWS EKS Cluster Created
AWS Node Groups Created
   ↓
terraform.tfstate Stored in S3
```

## Workflow Explanation

### 1. GitHub Checkout

Jenkins clones the Terraform code from the GitHub repository.

### 2. Terraform Init

Terraform initializes the working directory and downloads the required providers and modules.

### 3. Backend Configuration

Terraform reads the backend configuration from `main.tf` and connects to:

* S3 Bucket: `mindcircuit-eks-bucket`
* DynamoDB Table: `mc-eks-state-lock`

The S3 bucket stores the Terraform state file, and DynamoDB provides state locking to prevent concurrent modifications.

### 4. Terraform Plan

Terraform analyzes the configuration and generates an execution plan showing the resources that will be created or modified.

### 5. Read VPC Module

Terraform executes the VPC module and creates:

* VPC
* Public Subnets
* Private Subnets
* Internet Gateway
* Route Tables

### 6. Read EKS Module

Terraform executes the EKS module and creates:

* Amazon EKS Cluster
* Managed Node Groups
* IAM Roles
* Security Groups

### 7. Terraform Apply

Terraform provisions all AWS resources based on the generated execution plan.

### 8. State File Storage

After successful deployment, Terraform stores the `terraform.tfstate` file in the configured S3 bucket and uses DynamoDB for state locking.

## AWS Resources Created

* VPC
* Public Subnets
* Private Subnets
* Internet Gateway
* Route Tables
* EKS Cluster
* Managed Node Groups
* IAM Roles
* Security Groups
* S3 Backend State Storage
* DynamoDB State Lock Table

## Jenkins Pipeline Stages

1. Clone SCM
2. Install Terraform
3. Terraform Init
4. Terraform Plan
5. Terraform Apply
6. Retrieve Outputs
7. Clean Workspace

## Benefits of Remote Backend

* Centralized Terraform State Management
* State File Versioning
* Team Collaboration
* State Locking using DynamoDB
* Secure and Reliable Infrastructure Management
