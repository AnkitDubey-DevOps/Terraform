# Quick Reference Cheatsheet

---

# Essential Terraform Commands

```bash
terraform init
```

Initialize working directory and download providers.

---

```bash
terraform init -upgrade
```

Upgrade to latest allowed provider versions.

---

```bash
terraform plan
```

Preview infrastructure changes.

---

```bash
terraform plan -out=tfplan
```

Save execution plan to file.

---

```bash
terraform apply
```

Apply changes with confirmation.

---

```bash
terraform apply -auto-approve
```

Apply without confirmation (CI/CD only).

---

```bash
terraform apply tfplan
```

Apply a saved execution plan.

---

```bash
terraform destroy
```

Destroy all managed infrastructure.

---

```bash
terraform fmt
```

Format Terraform code.

---

```bash
terraform fmt -recursive
```

Format all subdirectories recursively.

---

```bash
terraform validate
```

Validate Terraform syntax and configuration.

---

```bash
terraform show
```

Display current state or plan details.

---

```bash
terraform output
```

Display Terraform outputs.

---

```bash
terraform state list
```

List resources stored in state.

---

```bash
terraform state show <resource>
```

Display detailed resource information.

---

```bash
terraform state rm <resource>
```

Remove resource from state without deleting cloud resource.

---

```bash
terraform import <resource> <id>
```

Import existing infrastructure into Terraform state.

---

```bash
terraform refresh
```

Deprecated method for syncing state with infrastructure.

---

```bash
terraform apply -refresh-only
```

Modern method to sync state with real infrastructure.

---

```bash
terraform workspace list
```

List all Terraform workspaces.

---

```bash
terraform workspace new dev
```

Create new workspace.

---

```bash
terraform graph
```

Generate dependency graph (DOT format).

---

# Variable Precedence (Highest → Lowest)

| Priority | Source |
|---|---|
| 1 | `-var` and `-var-file` CLI flags |
| 2 | `*.auto.tfvars` files |
| 3 | `terraform.tfvars` |
| 4 | `TF_VAR_*` environment variables |
| 5 | Default variable values |
| 6 | Interactive prompt |

---

# Terraform File Types

| File Type | Purpose |
|---|---|
| `.tf` | Main Terraform configuration files |
| `.tfvars` | Variable value files |
| `.tfstate` | Terraform state file |
| `.terraform/` | Provider plugins and modules |
| `.terraform.lock.hcl` | Dependency lock file |

---

# Key Meta-Arguments

| Meta-Argument | Purpose |
|---|---|
| `count` | Create multiple resources by number |
| `for_each` | Create multiple resources using maps/sets |
| `depends_on` | Explicit dependency declaration |
| `lifecycle` | Control create/update/destroy behavior |
| `provider` | Specify provider alias |

---

# Common Terraform Workflow

## Step 1 — Initialize

```bash
terraform init
```

---

## Step 2 — Validate

```bash
terraform validate
```

---

## Step 3 — Format Code

```bash
terraform fmt
```

---

## Step 4 — Preview Changes

```bash
terraform plan
```

---

## Step 5 — Apply Changes

```bash
terraform apply
```

---

# Useful Terraform Environment Variables

| Variable | Purpose |
|---|---|
| `TF_LOG` | Enable debug logging |
| `TF_LOG_PATH` | Write logs to file |
| `TF_VAR_*` | Pass variable values |
| `TF_INPUT` | Disable interactive prompts |
| `TF_WORKSPACE` | Select workspace automatically |

---

# Terraform Logging Levels

```bash
export TF_LOG=TRACE
```

Available levels:

- TRACE
- DEBUG
- INFO
- WARN
- ERROR

---

# Recommended `.gitignore`

```gitignore
.terraform/
*.tfstate
*.tfstate.backup
terraform.tfvars
crash.log
```

---

# Recommended CI/CD Commands

```bash
terraform fmt -check
terraform validate
terraform plan
```

---

# Common Terraform Best Practices

- Always review plans before apply
- Use remote state in teams
- Enable state locking
- Pin provider versions
- Use modules for reusable infrastructure
- Never hardcode secrets
- Use `for_each` over `count`
- Commit `.terraform.lock.hcl`
- Use separate state files per environment

---

# Terraform Resource Lifecycle Order

```text
Init → Validate → Plan → Apply → Destroy
```

---

# Common Backend Types

| Backend | Use Case |
|---|---|
| Local | Learning/testing |
| S3 | AWS remote state |
| AzureRM | Azure remote state |
| GCS | Google Cloud remote state |
| Terraform Cloud | Managed remote state |

---

# Terraform State Best Practices

- Never edit state manually
- Enable S3 versioning
- Enable DynamoDB locking
- Backup state regularly
- Restrict state file access

---

# Useful Third-Party Terraform Tools

| Tool | Purpose |
|---|---|
| `tflint` | Terraform linting |
| `checkov` | Security scanning |
| `tfsec` | Terraform security analysis |
| `terraform-docs` | Generate module docs |
| `tfenv` | Terraform version manager |

---

# Example Production Workflow

```bash
terraform fmt
terraform validate
terraform plan -out=tfplan
terraform apply tfplan
```

---

# Important Safety Rules

> ⚠️ Never run `terraform apply` blindly.

> ⚠️ Always inspect destroy (`-`) operations carefully.

> ⚠️ Never commit secrets or state files to Git.

> ⚠️ Always enable remote state locking in team environments.

---
