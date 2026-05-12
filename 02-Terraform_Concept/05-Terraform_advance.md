# High-Level Terraform Interview Questions

---

# 1. Explain Terraform's declarative approach vs procedural approach.

## Answer

Terraform uses a **declarative approach**, meaning you define the **desired end state** of infrastructure.

Example:

```text
"I want 3 EC2 instances."
```

Terraform determines:
- how to create them
- ordering
- dependencies
- updates

A **procedural approach** requires step-by-step instructions.

Example:
- create instance 1
- create instance 2
- configure networking
- attach storage

## Advantages of Declarative IaC

- Idempotent
- Easier maintenance
- Automatic dependency management
- Easier collaboration
- Safer infrastructure changes
- Minimal diff-based updates

---

# 2. How does Terraform manage dependencies between resources?

## Answer

Terraform automatically builds a:

```text
DAG (Directed Acyclic Graph)
```

based on resource references.

Example:

```hcl
subnet_id = aws_subnet.main.id
```

Terraform understands:
- subnet must exist first
- dependent resources are created afterward

## Benefits

- Parallel resource creation
- Correct ordering
- Automatic dependency resolution

## Explicit Dependency

```hcl
depends_on = [aws_subnet.main]
```

## Visualize Dependency Graph

```bash
terraform graph | dot -Tsvg > graph.svg
```

---

# 3. Difference between Terraform OSS, Terraform Cloud, and Terraform Enterprise?

## Answer

| Product | Description |
|---|---|
| Terraform OSS | Open-source CLI tool |
| Terraform Cloud (HCP Terraform) | SaaS platform with remote state and collaboration |
| Terraform Enterprise | Self-hosted enterprise version |

---

## Terraform OSS Features

- Free CLI
- Local execution
- Local/remote state
- Community providers

---

## Terraform Cloud Features

- Remote runs
- Team collaboration
- Policy enforcement
- Cost estimation
- VCS integration
- Private registry

---

## Terraform Enterprise Features

Everything in Terraform Cloud plus:
- Self-hosted deployment
- Advanced governance
- Enterprise integrations
- Air-gapped environments

---

# 4. How would you structure Terraform for a large enterprise?

## Answer

Use layered architecture.

## Recommended Layers

### Foundation Layer
- Accounts
- Networking
- IAM
- Security

### Shared Services Layer
- Logging
- Monitoring
- Databases

### Application Layer
- Team-specific workloads
- Microservices

---

## Best Practices

- Separate state files
- Shared modules
- Private module registry
- CI/CD pipelines
- Policy enforcement
- Environment isolation

---

# 5. Explain Terraform's plan/apply cycle.

## Answer

## Step 1 — `terraform plan`

Terraform:
- reads configuration
- reads current state
- queries cloud APIs
- calculates differences

Produces execution plan.

---

## Step 2 — `terraform apply`

Terraform executes approved changes.

---

## Why Safer Than Scripts?

Scripts:
- execute immediately
- may not be idempotent
- difficult to review

Terraform:
- previews changes first
- shows destroy/update/create actions
- minimizes unexpected modifications

---

# 6. How do you manage secrets securely in Terraform?

## Answer

Never hardcode secrets in:
- `.tf`
- `.tfvars`
- Git repositories

---

## Recommended Approaches

### Environment Variables

```bash
TF_VAR_db_password
```

### AWS Secrets Manager

Use data sources to fetch secrets.

### SSM Parameter Store

Store encrypted values.

### HashiCorp Vault

Dynamic secret generation.

---

## Mark Sensitive Data

```hcl
sensitive = true
```

---

## Encrypt State Files

State may contain:
- passwords
- tokens
- generated secrets

Always enable:
- backend encryption
- restricted access

---

# 7. What is Sentinel in Terraform Enterprise?

## Answer

Sentinel is a:

```text
Policy-as-Code framework
```

used in Terraform Cloud/Enterprise.

Policies run:
- after plan
- before apply

---

## Example Policies

- Enforce S3 encryption
- Restrict instance sizes
- Require mandatory tags
- Prevent public databases

---

## Policy Types

| Type | Behavior |
|---|---|
| Advisory | Warning only |
| Soft Mandatory | Can be overridden |
| Hard Mandatory | Blocks apply |

---

# 8. How do you handle Terraform state for zero-downtime deployments?

## Answer

Use:

```hcl
create_before_destroy = true
```

inside lifecycle blocks.

---

## Deployment Strategies

### Blue-Green Deployment
- Deploy new environment
- Switch traffic gradually
- Remove old environment later

### Rolling Updates
- Replace resources incrementally

---

## Best Practices

- Test in staging first
- Use load balancers
- Use DNS cutover
- Have rollback plans

---

# 9. How do you migrate existing infrastructure to Terraform?

## Answer

## Step-by-Step Migration

### 1. Write Matching Terraform Config

Infrastructure config must exactly match existing resources.

---

### 2. Import Resources

```bash
terraform import
```

---

### 3. Validate Drift

```bash
terraform plan
```

Goal:

```text
0 to add, 0 to change, 0 to destroy
```

---

### 4. Protect Critical Resources

```hcl
lifecycle {
  prevent_destroy = true
}
```

---

## Important

Importing does NOT recreate infrastructure.

It only updates Terraform state.

---

# 10. What are Terraform limitations and when would you choose another tool?

## Answer

## Terraform Limitations

- No built-in rollback
- State file bottlenecks
- Provisioners are fragile
- Weak OS/application configuration management

---

## When to Use Ansible

Best for:
- server configuration
- patching
- software installation
- OS management

---

## When to Use CloudFormation

Best for:
- AWS-only environments
- native AWS integrations

---

## When to Use Pulumi

Best for:
- developers preferring programming languages
- TypeScript/Python/Go workflows

---

## Terraform Strengths

- Multi-cloud support
- Huge provider ecosystem
- Strong community
- Excellent modularity
- Mature state management

---

# 11. How do you ensure production Terraform changes are peer-reviewed and auditable?

## Answer

Use:
- Pull Requests
- Branch protection rules
- CI/CD pipelines
- Terraform Cloud audit logs

---

## Recommended Workflow

1. Developer opens PR
2. CI runs:
   - fmt
   - validate
   - plan
3. Team reviews plan
4. Approval required
5. Merge triggers apply

---

## Additional Controls

- MFA for production access
- Separate production credentials
- Approval workflows
- Audit logging

---

# 12. How would you implement disaster recovery for Terraform state?

## Answer

## Recommended DR Strategy

### Enable S3 Versioning

Allows restoring older state versions.

---

### Enable MFA Delete

Protects against accidental deletion.

---

### Cross-Region Replication

Replicate state bucket to DR region.

---

### Periodic State Backups

```bash
terraform state pull > backup.tfstate
```

---

## Document Recovery Procedures

Include:
- restore process
- dependency order
- access controls
- rollback process

---
