# SECTION 6 — Advanced Terraform Concepts

## 6.1 count and for_each

Use `count` or `for_each` to create multiple instances of a resource without repeating code.

### count Example

```hcl
resource "aws_instance" "server" {
  count         = 3
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"

  tags = {
    Name = "Server-${count.index}"
  }
}
```

### for_each Example

```hcl
resource "aws_iam_user" "users" {
  for_each = toset(["alice", "bob", "charlie"])
  name     = each.value
}
```

> ⚠️ Prefer `for_each` over `count` for stable resource tracking.

---

## 6.2 Dynamic Blocks

Dynamic blocks generate repeated nested blocks programmatically.

```hcl
variable "ingress_rules" {
  default = [
    {
      from_port = 80
      to_port   = 80
      protocol  = "tcp"
    },
    {
      from_port = 443
      to_port   = 443
      protocol  = "tcp"
    }
  ]
}

resource "aws_security_group" "web" {
  name = "web-sg"

  dynamic "ingress" {
    for_each = var.ingress_rules

    content {
      from_port   = ingress.value.from_port
      to_port     = ingress.value.to_port
      protocol    = ingress.value.protocol
      cidr_blocks = ["0.0.0.0/0"]
    }
  }
}
```

---

## 6.3 Expressions & Functions

Terraform includes built-in functions for transforming data.

| Function | Description |
|---|---|
| `length(list)` | Count items |
| `merge(map1, map2)` | Merge maps |
| `toset(list)` | Convert list to set |
| `join(",", list)` | Join list into string |
| `split(",", string)` | Split string into list |
| `lookup(map, key, default)` | Get value with fallback |
| `file(path)` | Read file content |
| `jsonencode(object)` | Convert object to JSON |
| `coalesce(a,b,c)` | First non-null value |

---

## 6.4 Conditional Expressions

```hcl
resource "aws_instance" "server" {
  instance_type = var.environment == "prod" ? "t3.large" : "t2.micro"
  ami           = "ami-0c55b159cbfafe1f0"
}
```

### Conditional Resource Creation

```hcl
resource "aws_eip" "lb" {
  count    = var.enable_elastic_ip ? 1 : 0
  instance = aws_instance.server.id
}
```

---

## 6.5 Lifecycle Rules

Lifecycle rules control resource behavior.

```hcl
resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"

  lifecycle {
    create_before_destroy = true
    prevent_destroy       = true
    ignore_changes        = [tags]
  }
}
```

> ⚠️ Use `prevent_destroy = true` for critical resources.

---

## 6.6 depends_on

Explicit dependency declaration.

```hcl
resource "aws_s3_bucket_policy" "bucket_policy" {
  bucket = aws_s3_bucket.my_bucket.id
  policy = data.aws_iam_policy_document.s3_policy.json

  depends_on = [aws_s3_bucket.my_bucket]
}
```

---

## 6.7 Terraform Workspaces

Workspaces help manage multiple environments.

```bash
terraform workspace new dev
terraform workspace select prod
terraform workspace list
```

### Workspace Example

```hcl
resource "aws_instance" "server" {
  instance_type = terraform.workspace == "prod" ? "t3.large" : "t2.micro"
  ami           = "ami-0c55b159cbfafe1f0"
}
```

> 💡 Separate state files are often better than workspaces for large environments.

---

## 6.8 Provisioners

Provisioners run scripts after resource creation.

```hcl
resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"

  provisioner "remote-exec" {
    inline = [
      "sudo apt-get update",
      "sudo apt-get install -y nginx"
    ]

    connection {
      type        = "ssh"
      user        = "ubuntu"
      private_key = file("~/.ssh/id_rsa")
      host        = self.public_ip
    }
  }
}
```

> ⚠️ Provisioners should be a last resort.

---

## 6.9 terraform_remote_state Data Source

Access outputs from another Terraform state.

```hcl
data "terraform_remote_state" "network" {
  backend = "s3"

  config = {
    bucket = "my-terraform-states"
    key    = "network/terraform.tfstate"
    region = "us-east-1"
  }
}

resource "aws_instance" "app" {
  subnet_id     = data.terraform_remote_state.network.outputs.private_subnet_id
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
}
```

---

## 6.10 Terraform Import

Import existing infrastructure into Terraform.

```bash
terraform import aws_instance.my_server i-0abc12345def67890
```

### Import Into Module

```bash
terraform import module.vpc.aws_vpc.this vpc-0123456789abcdef0
```

### Terraform 1.5+ Import Block

```hcl
import {
  to = aws_instance.my_server
  id = "i-0abc12345def67890"
}
```

---

## 6.11 Moved Block

Rename resources without recreation.

```hcl
moved {
  from = aws_instance.old_name
  to   = aws_instance.new_name
}
```

---

## 6.12 Check Blocks (Terraform 1.5+)

Custom validation assertions.

```hcl
check "health_check" {

  data "http" "app_health" {
    url = "https://${aws_instance.web.public_ip}/health"
  }

  assert {
    condition     = data.http.app_health.status_code == 200
    error_message = "Application health check failed!"
  }
}
```

---

# SECTION 7 — Terraform in CI/CD Pipelines

## 7.1 Typical CI/CD Flow

1. Developer changes `.tf` files and opens PR
2. CI pipeline runs:
   - `terraform fmt -check`
   - `terraform validate`
   - `terraform plan`
3. Plan output reviewed by team
4. Merge to main triggers:
   - `terraform apply`

---

## 7.2 GitHub Actions Example

```yaml
name: Terraform CI/CD

on:
  push:
    branches: [main]

  pull_request:

jobs:
  terraform:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3

      - uses: hashicorp/setup-terraform@v2
        with:
          terraform_version: 1.6.0

      - name: Terraform Init
        run: terraform init

      - name: Terraform Format Check
        run: terraform fmt -check

      - name: Terraform Plan
        run: terraform plan -out=tfplan

      - name: Terraform Apply
        if: github.ref == 'refs/heads/main'
        run: terraform apply -auto-approve tfplan
```

---

## 7.3 Terraform Cloud / HCP Terraform

Terraform Cloud provides:

- Remote execution
- Remote state management
- Team access control
- Cost estimation
- Policy enforcement
- Private module registry

### Features

#### Remote Execution
Plans/applies run remotely.

#### VCS Integration
Auto-trigger runs on git push.

#### Sentinel Policies
Enforce governance rules.

#### Cost Estimation
Estimate cloud cost before apply.

#### Private Registry
Host internal reusable modules.

---

# SECTION 8 — Terraform Best Practices

## 8.1 Project Structure

```text
project/
├── environments/
│   ├── dev/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── terraform.tfvars
│   └── prod/
│       ├── main.tf
│       └── terraform.tfvars
└── modules/
    ├── vpc/
    ├── ec2/
    └── rds/
```

---

## 8.2 Security Best Practices

- Never store secrets in `.tf` files
- Use environment variables (`TF_VAR_*`)
- Use vault integrations
- Add `.tfvars` and `.tfstate` to `.gitignore`
- Enable encryption on remote state backends
- Use IAM roles instead of long-lived keys
- Enable MFA delete on S3 buckets

---

## 8.3 Code Quality Practices

- Run `terraform fmt` before commits
- Run `terraform validate` in CI
- Use `tflint` for linting
- Use `checkov` or `tfsec` for security scanning
- Add descriptions to variables/outputs
- Use meaningful resource names

---

## 8.4 State Management Best Practices

- Always use remote state in teams
- Enable state locking
- Separate state files per environment
- Enable S3 versioning
- Avoid `terraform state rm` unless necessary

---
