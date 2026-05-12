# 🚀 Terraform: Complete Guide

---

# SECTION 1 — Introduction to Terraform

## 1.1 What is Terraform?

Terraform is an open-source Infrastructure as Code (IaC) tool developed by HashiCorp.

It allows you to define, provision, and manage cloud infrastructure using a declarative configuration language called HCL (HashiCorp Configuration Language).

Instead of clicking buttons in a cloud console, you write code that describes the desired state of your infrastructure.

---

## 1.2 Why Use Terraform?

- Multi-cloud support — works with AWS, Azure, GCP, and 1000+ providers
- Declarative syntax — describe WHAT you want, not HOW
- State management — Terraform tracks infrastructure in a state file
- Idempotent — same configuration gives the same result repeatedly
- Version controlled — infrastructure treated like application code
- Collaboration — teams can review and apply changes together

---

## 1.3 How Terraform Works (Core Workflow)

Terraform follows a simple 3-step workflow:

### 1. Write

Define infrastructure in `.tf` files using HCL.

### 2. Plan

Preview changes Terraform will make.

```bash
terraform plan
```

### 3. Apply

Create/update/destroy infrastructure.

```bash
terraform apply
```

---

## 1.4 Terraform vs Other IaC Tools

| Tool | Language | Approach | Best For |
|---|---|---|---|
| Terraform | HCL | Declarative | Multi-cloud |
| Ansible | YAML | Procedural | Configuration management |
| CloudFormation | JSON/YAML | Declarative | AWS only |
| Pulumi | Python/JS | Imperative | Developers preferring code |

---

# SECTION 2 — Installation & Setup

## 2.1 Installing Terraform

### macOS (Homebrew)

```bash
brew tap hashicorp/tap
brew install hashicorp/tap/terraform
```

### Ubuntu / Debian

```bash
wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp.gpg

sudo apt-add-repository 'deb [signed-by=/usr/share/keyrings/hashicorp.gpg] https://apt.releases.hashicorp.com focal main'

sudo apt update && sudo apt install terraform
```

### Verify Installation

```bash
terraform -version
```

---

## 2.2 Essential Terraform Commands

| Command | Description |
|---|---|
| `terraform init` | Initialize working directory |
| `terraform plan` | Preview infrastructure changes |
| `terraform apply` | Apply changes |
| `terraform destroy` | Destroy infrastructure |
| `terraform fmt` | Format Terraform code |
| `terraform validate` | Validate syntax |
| `terraform show` | Show current state |
| `terraform output` | Display output values |
| `terraform state` | Advanced state operations |

---

# SECTION 3 — Terraform Basics

## 3.1 HCL Syntax Fundamentals

HCL is designed to be human-readable and machine-friendly.

### Basic HCL Structure

```hcl
<BLOCK_TYPE> "<BLOCK_LABEL>" "<BLOCK_LABEL>" {
  argument = value
}
```

### Example: AWS EC2 Instance

```hcl
resource "aws_instance" "my_server" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"

  tags = {
    Name = "MyServer"
  }
}
```

---

## 3.2 Providers

Providers are plugins that let Terraform interact with cloud services.

### AWS Provider Example

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}
```

> ⚠️ Always pin provider versions to avoid unexpected breaking upgrades.

---

## 3.3 Resources

Resources are the core building blocks in Terraform.

### Example: S3 Bucket

```hcl
resource "aws_s3_bucket" "my_bucket" {
  bucket = "my-unique-bucket-name-2024"

  tags = {
    Environment = "Dev"
    Team        = "DevOps"
  }
}
```

---

## 3.4 Variables

Variables make configurations reusable and flexible.

### Declare Variable

```hcl
variable "instance_type" {
  description = "EC2 instance type"
  type        = string
  default     = "t2.micro"
}
```

### Use Variable

```hcl
resource "aws_instance" "web" {
  instance_type = var.instance_type
  ami           = "ami-0c55b159cbfafe1f0"
}
```

### Supported Variable Types

| Type | Example |
|---|---|
| string | `"us-east-1"` |
| number | `3` |
| bool | `true` |
| list | `["a", "b"]` |
| map | `{env = "dev"}` |
| object | Complex structured type |
| any | Any type |

---

## 3.5 Outputs

Outputs expose values after deployment.

```hcl
output "instance_public_ip" {
  description = "Public IP of the web server"
  value       = aws_instance.web.public_ip
}
```

### Sensitive Output

```hcl
output "db_password" {
  value     = var.db_password
  sensitive = true
}
```

---

## 3.6 Data Sources

Data sources fetch existing infrastructure information.

### Example: Existing VPC

```hcl
data "aws_vpc" "existing" {
  filter {
    name   = "tag:Name"
    values = ["production-vpc"]
  }
}
```

### Use Data Source

```hcl
resource "aws_subnet" "example" {
  vpc_id     = data.aws_vpc.existing.id
  cidr_block = "10.0.1.0/24"
}
```

---

## 3.7 Locals

Locals help avoid repetition.

```hcl
locals {
  common_tags = {
    Project     = "my-app"
    Environment = var.environment
    ManagedBy   = "Terraform"
  }

  name_prefix = "${var.project}-${var.environment}"
}

resource "aws_instance" "web" {
  ami  = "ami-0c55b159cbfafe1f0"
  tags = local.common_tags
}
```

---

# SECTION 4 — State Management

## 4.1 What is Terraform State?

Terraform state is a JSON file (`terraform.tfstate`) that maps your configuration to real-world resources.

Terraform uses it to determine:
- What exists
- What changed
- What must be created or destroyed

> ⚠️ Never manually edit the state file unless absolutely necessary.

---

## 4.2 Local vs Remote State

### Local State

Stored locally in:

```text
terraform.tfstate
```

Good for learning, not for teams.

### Remote State

Stored in:
- AWS S3
- Azure Blob
- Google Cloud Storage
- Terraform Cloud

Recommended for collaboration.

---

## 4.3 Remote Backend (S3 + DynamoDB)

```hcl
terraform {
  backend "s3" {
    bucket         = "my-terraform-state-bucket"
    key            = "prod/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "terraform-state-lock"
  }
}
```

> 💡 Always enable state locking with DynamoDB.

---

## 4.4 State Commands

```bash
terraform state list
terraform state show <resource>
terraform state mv
terraform state rm
terraform import
```

---

## 4.5 State Locking

State locking prevents concurrent operations from corrupting infrastructure state.

When one user runs `plan` or `apply`, Terraform locks the state until completion.

---

# SECTION 5 — Modules

## 5.1 What are Modules?

Modules are reusable packages of Terraform configurations.

Think of modules like functions in programming.

---

## 5.2 Module Structure

```text
my-module/
├── main.tf
├── variables.tf
├── outputs.tf
└── README.md
```

---

## 5.3 Creating a Module

### `modules/ec2/main.tf`

```hcl
resource "aws_instance" "server" {
  ami           = var.ami
  instance_type = var.instance_type

  tags = {
    Name = var.name
  }
}
```

### `modules/ec2/variables.tf`

```hcl
variable "ami" {
  type = string
}

variable "instance_type" {
  type    = string
  default = "t2.micro"
}

variable "name" {
  type = string
}
```

### `modules/ec2/outputs.tf`

```hcl
output "instance_id" {
  value = aws_instance.server.id
}

output "public_ip" {
  value = aws_instance.server.public_ip
}
```

---

## 5.4 Calling a Module

### `root/main.tf`

```hcl
module "web_server" {
  source        = "./modules/ec2"
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.small"
  name          = "WebServer"
}
```

### Use Module Output

```hcl
output "web_ip" {
  value = module.web_server.public_ip
}
```

---

## 5.5 Public Registry Modules

Terraform Registry contains thousands of reusable modules.

### Example: Public VPC Module

```hcl
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "5.1.2"

  name = "my-vpc"
  cidr = "10.0.0.0/16"

  azs             = ["us-east-1a", "us-east-1b"]
  private_subnets = ["10.0.1.0/24", "10.0.2.0/24"]
  public_subnets  = ["10.0.101.0/24", "10.0.102.0/24"]
}
```
