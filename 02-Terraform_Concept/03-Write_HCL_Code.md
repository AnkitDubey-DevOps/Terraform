# TIPS TO WRITE GREAT HCL CODE [VERY IMPORTANT]

This section is critically important. Writing clean, well-structured HCL is what separates a beginner from a professional Terraform engineer.

Master these tips to write production-quality code.

---

# TIP 1 — Always Use `terraform fmt`

The `terraform fmt` command automatically formats your code to HashiCorp's canonical style.

It aligns equal signs, fixes indentation, and normalizes whitespace.

Make it a habit to run this before every commit.

```bash
terraform fmt
terraform fmt -recursive
terraform fmt -check
```

> ⚠️ Add `terraform fmt -check` to CI pipelines to reject improperly formatted PRs.

---

# TIP 2 — Use `terraform validate`

Before planning or applying, always validate your configuration.

It catches:
- Syntax errors
- Missing arguments
- Invalid references

```bash
terraform validate
```

> ⚠️ `terraform validate` catches errors BEFORE hitting cloud APIs.

---

# TIP 3 — Keep Variables Well-Documented

Every variable should include:
- Description
- Type
- Sensible default
- Validation

## ❌ Bad Example

```hcl
variable "size" {}
```

## ✅ Good Example

```hcl
variable "instance_type" {
  description = "EC2 instance type for the web server"
  type        = string
  default     = "t2.micro"

  validation {
    condition = contains(
      ["t2.micro", "t2.small", "t3.medium", "t3.large"],
      var.instance_type
    )

    error_message = "Must be a valid instance type."
  }
}
```

> ⚠️ Validation blocks provide clear user-friendly errors.

---

# TIP 4 — Use `locals` to Avoid Repetition (DRY Principle)

Avoid repeating values across resources.

```hcl
locals {
  app_name    = "payment-service"
  environment = var.env

  full_name = "${local.app_name}-${local.environment}"

  common_tags = {
    Application = local.app_name
    Environment = local.environment
    ManagedBy   = "Terraform"
    Owner       = "platform-team"
  }
}
```

---

# TIP 5 — Use Consistent Naming Conventions

## Recommended Conventions

- Use `snake_case`
- Prefix names with environment
- Use descriptive resource names
- Use meaningful provider aliases

### Example

```hcl
aws_security_group.web_server_sg
```

Better than:

```hcl
aws_security_group.sg1
```

> ⚠️ Consistent naming improves maintainability in large teams.

---

# TIP 6 — Separate Files by Purpose

Terraform reads all `.tf` files together.

Organize by responsibility:

```text
main.tf
variables.tf
outputs.tf
providers.tf
versions.tf
locals.tf
data.tf
```

> ⚠️ Avoid giant monolithic `main.tf` files.

---

# TIP 7 — Pin Provider and Module Versions

Never allow uncontrolled upgrades.

```hcl
terraform {
  required_version = ">= 1.5.0, < 2.0.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}
```

## Meaning of `~> 5.0`

```text
>= 5.0 and < 6.0
```

> ⚠️ Allows minor updates but blocks breaking major upgrades.

---

# TIP 8 — Use `for_each` Instead of `count`

## ❌ Fragile `count`

```hcl
resource "aws_iam_user" "users" {
  count = 3
  name  = var.user_names[count.index]
}
```

## ✅ Preferred `for_each`

```hcl
resource "aws_iam_user" "users" {
  for_each = toset(var.user_names)
  name     = each.value
}
```

> ⚠️ `for_each` prevents unwanted recreation caused by index shifting.

---

# TIP 9 — Sensitive Variables and Outputs

Always mark secrets as sensitive.

```hcl
variable "db_password" {
  description = "Database master password"
  type        = string
  sensitive   = true
}
```

### Sensitive Output

```hcl
output "db_connection_string" {
  value     = "postgres://admin:${var.db_password}@${aws_db_instance.db.endpoint}"
  sensitive = true
}
```

> ⚠️ Prevent accidental exposure in logs and CI pipelines.

---

# TIP 10 — Use `.tfvars` Files Per Environment

## dev.tfvars

```hcl
environment   = "dev"
instance_type = "t2.micro"
db_size       = "db.t3.small"
```

## prod.tfvars

```hcl
environment   = "prod"
instance_type = "t3.large"
db_size       = "db.r5.xlarge"
```

## Apply with Specific Variables

```bash
terraform apply -var-file="prod.tfvars"
```

---

# TIP 11 — Always Add Tags to Cloud Resources

Tags help with:
- Cost allocation
- Security auditing
- Ownership tracking
- Compliance

> ⚠️ Most organizations mandate tagging standards.

---

# TIP 12 — Use Variable Validation Blocks

Catch bad input early.

```hcl
variable "environment" {
  type = string

  validation {
    condition = contains(
      ["dev", "staging", "prod"],
      var.environment
    )

    error_message = "Environment must be dev, staging, or prod."
  }
}
```

---

# TIP 13 — Write a README for Every Module

A module README should include:
- Purpose
- Inputs
- Outputs
- Usage examples

## Auto-generate Docs

```bash
terraform-docs markdown . > README.md
```

---

# TIP 14 — Use `tflint` and `checkov`

## Tools

- `tflint` → Provider-specific linting
- `checkov` → Security scanning
- `tfsec` → Terraform security analysis

## Commands

```bash
tflint --init && tflint
checkov -d .
```

> ⚠️ Integrate these tools into CI pipelines.

---

# TIP 15 — Never Run `terraform apply` Without Reviewing Plan

Always inspect the plan before applying.

```bash
terraform plan -out=tfplan
terraform apply tfplan
```

## Watch Carefully For

- `-` → Destroy operations
- `~` → In-place modifications

> ⚠️ Accidental destruction of production resources can be irreversible.

---
