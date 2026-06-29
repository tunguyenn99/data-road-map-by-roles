# 🏗️ Terraform — Learning Guide (Beginner → Advanced)

## Tổng quan

Terraform là Infrastructure as Code (IaC) tool. Quản lý cloud resources (Redshift clusters, S3 buckets, IAM roles, VPCs...) bằng code thay vì click console.

**Dùng cho:** BI Engineer (Senior), DE, Platform Eng  
**Why:** Reproducible infrastructure, version-controlled, team collaboration

---

## Level 1: Beginner (1 tuần)

### Core Concepts
- **Provider**: cloud platform (AWS, GCP, Azure)
- **Resource**: infrastructure component (EC2, S3, Redshift)
- **State**: current infrastructure status (terraform.tfstate)
- **Plan**: preview changes before applying
- **Apply**: execute changes
- **Destroy**: tear down resources

### First Configuration
```hcl
# main.tf
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = "ap-southeast-1"  # Singapore
}

# Create S3 bucket
resource "aws_s3_bucket" "data_lake" {
  bucket = "company-data-lake-dev"
  tags = {
    Environment = "dev"
    Team        = "data-engineering"
  }
}

# Create IAM role
resource "aws_iam_role" "redshift_role" {
  name = "redshift-spectrum-role"
  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Action = "sts:AssumeRole"
      Effect = "Allow"
      Principal = { Service = "redshift.amazonaws.com" }
    }]
  })
}
```

### Essential Commands
```bash
terraform init      # Initialize, download providers
terraform plan      # Preview changes (dry-run)
terraform apply     # Apply changes (creates/modifies resources)
terraform destroy   # Destroy all managed resources
terraform fmt       # Format code
terraform validate  # Validate syntax
terraform state list  # List managed resources
```

### Bài tập
1. Install Terraform, create S3 bucket
2. Modify bucket (add tags), run plan → apply
3. Destroy bucket
4. Create IAM role + policy
5. Use `terraform state list` to inspect state

### Resources
| Resource | Link | Type |
|---|---|---|
| Terraform Get Started (AWS) | [developer.hashicorp.com/terraform/tutorials/aws-get-started](https://developer.hashicorp.com/terraform/tutorials/aws-get-started) | Free |
| Terraform Docs | [developer.hashicorp.com/terraform/docs](https://developer.hashicorp.com/terraform/docs) | Free |
| HashiCorp Learn | [developer.hashicorp.com/terraform/tutorials](https://developer.hashicorp.com/terraform/tutorials) | Free |

---

## Level 2: Intermediate (3 tuần)

### Variables & Outputs
```hcl
# variables.tf
variable "environment" {
  description = "Deployment environment"
  type        = string
  default     = "dev"
}

variable "redshift_node_type" {
  type    = string
  default = "ra3.xlplus"
}

# main.tf
resource "aws_redshift_cluster" "analytics" {
  cluster_identifier = "analytics-${var.environment}"
  node_type          = var.redshift_node_type
  number_of_nodes    = var.environment == "prod" ? 3 : 1
  # ...
}

# outputs.tf
output "redshift_endpoint" {
  value = aws_redshift_cluster.analytics.endpoint
}
```

### Modules (reusable components)
```hcl
# modules/redshift/main.tf
resource "aws_redshift_cluster" "this" {
  cluster_identifier = var.cluster_name
  node_type          = var.node_type
  # ...
}

# Usage in root
module "redshift_dev" {
  source       = "./modules/redshift"
  cluster_name = "analytics-dev"
  node_type    = "ra3.xlplus"
}

module "redshift_prod" {
  source       = "./modules/redshift"
  cluster_name = "analytics-prod"
  node_type    = "ra3.4xlarge"
}
```

### Data Sources & Dependencies
```hcl
# Reference existing resources
data "aws_vpc" "existing" {
  filter {
    name   = "tag:Name"
    values = ["company-vpc"]
  }
}

resource "aws_redshift_subnet_group" "main" {
  name       = "redshift-subnet-group"
  subnet_ids = data.aws_vpc.existing.subnet_ids
}
```

### Remote State
```hcl
# Store state in S3 (team collaboration)
terraform {
  backend "s3" {
    bucket = "company-terraform-state"
    key    = "redshift/terraform.tfstate"
    region = "ap-southeast-1"
    encrypt = true
    dynamodb_table = "terraform-locks"
  }
}
```

### Bài tập
1. Parameterize config with variables (dev vs prod)
2. Create module for Redshift cluster
3. Use remote state (S3 backend)
4. Import existing resource into Terraform state
5. Use data sources to reference existing VPC

### Resources
| Resource | Link | Type |
|---|---|---|
| Terraform Modules | [developer.hashicorp.com/terraform/tutorials/modules](https://developer.hashicorp.com/terraform/tutorials/modules) | Free |
| AWS Provider docs | [registry.terraform.io/providers/hashicorp/aws](https://registry.terraform.io/providers/hashicorp/aws/latest/docs) | Free |

---

## Level 3: Advanced (4 tuần)

### Workspaces (multi-environment)
```bash
terraform workspace new dev
terraform workspace new prod
terraform workspace select dev
terraform workspace list

# Use in config
resource "aws_redshift_cluster" "main" {
  cluster_identifier = "analytics-${terraform.workspace}"
  number_of_nodes    = terraform.workspace == "prod" ? 3 : 1
}
```

### CI/CD for Terraform
```yaml
# .gitlab-ci.yml
stages:
  - validate
  - plan
  - apply

validate:
  script:
    - terraform init
    - terraform validate
    - terraform fmt -check

plan:
  script:
    - terraform plan -out=tfplan
  artifacts:
    paths: [tfplan]

apply:
  stage: apply
  script:
    - terraform apply tfplan
  when: manual  # Require approval
  only: [main]
```

### Advanced Patterns
```hcl
# for_each (create multiple similar resources)
variable "schemas" {
  default = ["staging", "golden", "platinum"]
}

resource "aws_redshift_schema" "schemas" {
  for_each = toset(var.schemas)
  name     = each.value
  database = "analytics_db"
}

# Lifecycle rules
resource "aws_redshift_cluster" "main" {
  # ...
  lifecycle {
    prevent_destroy = true  # Safety: can't destroy prod cluster
    ignore_changes  = [master_password]  # Don't track password changes
  }
}

# Provisioners (last resort)
resource "null_resource" "db_setup" {
  provisioner "local-exec" {
    command = "psql -h ${aws_redshift_cluster.main.endpoint} -f setup.sql"
  }
}
```

### Terragrunt (DRY Terraform)
```hcl
# terragrunt.hcl (wrapper for multi-env)
include "root" {
  path = find_in_parent_folders()
}

terraform {
  source = "../../modules/redshift"
}

inputs = {
  environment = "dev"
  node_type   = "ra3.xlplus"
}
```

### Bài tập
1. Set up CI/CD pipeline for Terraform (plan on PR, apply on merge)
2. Use `for_each` to create RBAC roles/grants
3. Implement workspaces for dev/uat/prod
4. Write Terragrunt config for multi-environment
5. Import existing Redshift cluster into Terraform management

### Resources
| Resource | Link | Type |
|---|---|---|
| Terraform Associate Cert | [developer.hashicorp.com/certifications](https://developer.hashicorp.com/certifications/infrastructure-automation) | Paid |
| Terragrunt | [terragrunt.gruntwork.io](https://terragrunt.gruntwork.io/) | Free |
| Terraform Up & Running (book) | Yevgeniy Brikman (3rd Ed) | Paid |
| Spacelift Blog | [spacelift.io/blog](https://spacelift.io/blog) | Free |

---
## Alternatives & Công cụ tương đương

| Tool | Description | vs Terraform |
|---|---|---|
| **Pulumi** | IaC with real programming languages | Python/TS/Go instead of HCL |
| **AWS CloudFormation** | AWS-native IaC | AWS-only, JSON/YAML |
| **AWS CDK** | CloudFormation with code | TypeScript/Python, AWS-only |
| **Crossplane** | Kubernetes-native IaC | GitOps, K8s-first |
| **OpenTofu** | Open-source Terraform fork | Same HCL, truly open |
| **Ansible** | Configuration management | Server config, not infra creation |
| **Terragrunt** | Terraform wrapper (DRY) | Multi-env, keep Terraform DRY |

### Khi nào chọn gì?
- **Terraform/OpenTofu** → Multi-cloud, industry standard
- **Pulumi** → Prefer real programming languages over HCL
- **CloudFormation/CDK** → All-in on AWS
- **Crossplane** → Kubernetes-native teams
- **Terragrunt** → Large Terraform projects, DRY config
