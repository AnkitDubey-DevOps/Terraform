# 50 Scenario-Based Terraform Questions & Answers

# Q1. Your colleague ran `terraform apply` and now the state file shows resources that don't exist in the cloud. What do you do?

### Answer

Use:

```bash
terraform state rm <resource>
```

to remove orphaned entries from state, or run:

```bash
terraform refresh
```

to sync state with actual infrastructure.

Investigate why the resources disappeared and decide whether to recreate them or clean up the state.

---

# Q2. You need to create 5 S3 buckets with different names from a list variable. How do you do it?

### Answer

Use `for_each` with `toset()`.

```hcl
resource "aws_s3_bucket" "buckets" {
  for_each = toset(var.bucket_names)

  bucket = each.value
}
```

This creates one bucket per name and avoids unnecessary recreation.

---

# Q3. You accidentally deleted a `terraform.tfstate` file. What now?

### Answer

- If using remote state → restore from S3 versioning
- If using local state → re-import resources using:

```bash
terraform import
```

This is why remote state with versioning is critical.

---

# Q4. Two developers ran `terraform apply` simultaneously and corrupted state. How do you prevent this?

### Answer

Enable state locking using DynamoDB.

```hcl
backend "s3" {
  dynamodb_table = "terraform-state-lock"
}
```

Terraform locks state during plan/apply operations.

---

# Q5. You need to create an EC2 instance only in production. How?

### Answer

Use conditional `count`.

```hcl
resource "aws_instance" "prod_server" {
  count = var.environment == "prod" ? 1 : 0
}
```

---

# Q6. You renamed a resource but don't want recreation. What do you do?

### Answer

Use a `moved` block.

```hcl
moved {
  from = aws_instance.old_name
  to   = aws_instance.new_name
}
```

---

# Q7. You want to import an existing RDS database into Terraform. How?

### Answer

Write the matching resource block first.

Then run:

```bash
terraform import aws_db_instance.my_db <db-id>
```

Finally:

```bash
terraform plan
```

to verify no drift exists.

---

# Q8. Your plan shows destruction of a production DB. What should you do?

### Answer

STOP immediately.

Investigate:
- accidental rename
- removed resource block
- immutable attribute changes

Protect critical resources using:

```hcl
lifecycle {
  prevent_destroy = true
}
```

---

# Q9. You need to share a VPC ID across Terraform projects. How?

### Answer

Use `terraform_remote_state`.

```hcl
data "terraform_remote_state" "network" {
  backend = "s3"
}
```

Then access:

```hcl
data.terraform_remote_state.network.outputs.vpc_id
```

---

# Q10. You need to run a shell script after EC2 creation. How?

### Answer

Use a `remote-exec` provisioner.

```hcl
provisioner "remote-exec" {
  inline = [
    "sudo apt update",
    "sudo apt install nginx -y"
  ]
}
```

> ⚠️ Prefer `user_data` or configuration management tools instead.

---

# Q11. How do you manage secrets securely in Terraform?

### Answer

Never hardcode secrets.

Use:
- `TF_VAR_*`
- AWS Secrets Manager
- SSM Parameter Store
- HashiCorp Vault

Mark secrets as sensitive:

```hcl
sensitive = true
```

---

# Q12. Your module needs different behavior for dev vs prod. How?

### Answer

Use conditional expressions.

```hcl
instance_type = var.environment == "prod" ? "t3.large" : "t2.micro"
```

Or use separate `.tfvars` files.

---

# Q13. How do you enforce encryption on all S3 buckets?

### Answer

Use:
- Sentinel policies
- Checkov
- tfsec
- CI/CD validation

---

# Q14. How do you safely upgrade a provider from 4.x to 5.x?

### Answer

1. Update version constraint
2. Run:

```bash
terraform init -upgrade
```

3. Review:

```bash
terraform plan
```

4. Read provider changelog
5. Test in non-production first

---

# Q15. Your Terraform state has 100+ resources and plans are slow. Fix?

### Answer

Split infrastructure into smaller state files.

Example:
- networking/
- compute/
- database/
- security/

Use `terraform_remote_state` for sharing outputs.

---

# Q16. How do you test Terraform modules?

### Answer

Use:
- `terraform test`
- Terratest
- checkov
- terraform-compliance

---

# Q17. How do you prevent automatic destructive applies in CI/CD?

### Answer

Require manual approval when plans include destroys.

Use:

```bash
terraform plan -out=tfplan
```

and inspect destroy operations before apply.

---

# Q18. A teammate manually changed AWS resources causing drift. What do you do?

### Answer

Run:

```bash
terraform plan
```

Then either:
- update Terraform config
- or re-apply Terraform to revert drift

Enforce infrastructure changes through Terraform only.

---

# Q19. How do you deploy infrastructure in multiple AWS regions?

### Answer

Use provider aliases.

```hcl
provider "aws" {
  alias  = "us_east"
  region = "us-east-1"
}

provider "aws" {
  alias  = "eu_west"
  region = "eu-west-1"
}
```

---

# Q20. How do you manage Terraform in large teams?

### Answer

Use:
- remote state
- state locking
- modules
- CI/CD pipelines
- Terraform Cloud
- PR reviews

---

# Q21. Difference between `terraform taint` and `-replace`?

### Answer

Old:

```bash
terraform taint
```

Modern:

```bash
terraform apply -replace=aws_instance.web
```

Both force recreation.

---

# Q22. How do you pass multiple security group IDs?

### Answer

```hcl
variable "sg_ids" {
  type = list(string)
}
```

Usage:

```hcl
vpc_security_group_ids = var.sg_ids
```

---

# Q23. Create resources only if variable is true?

### Answer

```hcl
count = var.enable_feature ? 1 : 0
```

---

# Q24. Create dynamic ingress rules?

### Answer

Use dynamic blocks.

```hcl
dynamic "ingress" {
  for_each = var.ingress_rules
}
```

---

# Q25. What happens if you remove a resource block?

### Answer

Terraform plans to destroy it.

To stop management without deletion:

```bash
terraform state rm
```

---

# Q26. Reuse same module across environments?

### Answer

Use:
- separate `.tfvars`
- separate environment directories
- multiple module calls

---

# Q27. `terraform init` fails downloading providers. Fix?

### Answer

Check:
- internet access
- proxy configuration
- provider mirrors
- local filesystem mirrors

---

# Q28. Output map of instance IDs from `for_each`?

### Answer

```hcl
output "instance_ids" {
  value = {
    for k, v in aws_instance.servers : k => v.id
  }
}
```

---

# Q29. Reference attributes from `for_each` resources?

### Answer

```hcl
aws_instance.servers["server_name"].public_ip
```

---

# Q30. What is `null_resource`?

### Answer

A Terraform resource that manages no infrastructure but supports:
- provisioners
- triggers
- local commands

---

# Q31. How do you roll back a bad Terraform apply?

### Answer

Terraform has no native rollback.

Rollback process:
1. Restore old config
2. Restore previous state version
3. Run `terraform apply`

---

# Q32. Provider not in public registry. What do you do?

### Answer

Use:
- local filesystem mirrors
- private provider registries
- custom-built providers

---

# Q33. Generate random resource names?

### Answer

Use the random provider.

```hcl
resource "random_id" "suffix" {
  byte_length = 4
}
```

---

# Q34. Difference between `count.index` and `each.key`?

### Answer

| count.index | each.key |
|---|---|
| Numeric index | Stable key |
| Fragile ordering | Stable mapping |
| Used with `count` | Used with `for_each` |

---

# Q35. Force recreation without changing config?

### Answer

```bash
terraform apply -replace=aws_instance.web
```

---

# Q36. Terraform with Docker?

### Answer

Use Docker provider.

Terraform can manage:
- images
- containers
- networks
- volumes

---

# Q37. `0 to add, 0 to change, 0 to destroy` — is infra definitely correct?

### Answer

Not always.

Terraform only tracks managed resources in state.

Manual changes outside Terraform may still exist.

---

# Q38. Structure Terraform for microservices?

### Answer

Use:
- one module per service
- shared networking state
- service-specific state files

---

# Q39. Manage Terraform version upgrades across teams?

### Answer

Use:
- `tfenv`
- pinned versions
- gradual upgrades
- testing environments

---

# Q40. Enable verbose Terraform debugging?

### Answer

```bash
export TF_LOG=DEBUG
```

Options:
- TRACE
- DEBUG
- INFO
- WARN
- ERROR

---

# Q41. Purpose of `.terraform.lock.hcl`?

### Answer

Dependency lock file storing:
- provider versions
- checksums

Always commit it to Git.

---

# Q42. Handle circular dependencies?

### Answer

Refactor infrastructure.

Terraform does not allow circular dependencies.

---

# Q43. Infrastructure across multiple AWS accounts?

### Answer

Use provider aliases with `assume_role`.

```hcl
provider "aws" {
  alias = "prod"
}
```

---

# Q44. Terraform with Kubernetes?

### Answer

Use:
- Kubernetes provider
- Helm provider

Typical flow:
1. Create cluster
2. Deploy workloads

---

# Q45. Prevent argument changes from forcing replacement?

### Answer

Use:

```hcl
lifecycle {
  ignore_changes = [user_data]
}
```

---

# Q46. State contains manually deleted resource. Fix?

### Answer

Run:

```bash
terraform plan
```

Then either:
- recreate resource
- or remove from state

---

# Q47. Share modules across teams?

### Answer

Use:
- Terraform private registry
- shared Git repositories
- version tagging

---

# Q48. Handle Terraform timeouts?

### Answer

Use:

```hcl
timeouts {
  create = "30m"
  delete = "20m"
}
```

---

# Q49. Difference between `terraform refresh` and `-refresh-only`?

### Answer

| Command | Status |
|---|---|
| `terraform refresh` | Deprecated |
| `terraform apply -refresh-only` | Recommended |

---

# Q50. Debug `inconsistent result after apply`?

### Answer

Possible causes:
- provider normalization
- API formatting changes
- unsupported values

Debug using:

```bash
export TF_LOG=TRACE
```

Review provider/API responses carefully.

---
