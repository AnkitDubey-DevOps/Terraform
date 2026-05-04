**Q61) What is a null_resource?**  
**Ans:** It's a resource that doesn't create anything real but can run provisioners or act as a dependency placeholder.

**Q62) What is terraform version command?**  
**Ans:** It shows which version of Terraform you have installed.

**Q63) How do you specify a required Terraform version?**  
**Ans:** Use the required_version argument in the terraform block:

```hcl
terraform {
  required_version = ">= 1.0.0"
}
```

**Q64) How do you specify a required provider version?**  
**Ans:** In the required_providers block:

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.0"
    }
  }
}
```

**Q65) What does ~> mean in version constraints?**  
**Ans:** It means "compatible with." For example, ~> 4.0 allows 4.0, 4.1, 4.9, but not 5.0.

**Q66) What is Terraform Cloud?**  
**Ans:** A service by HashiCorp that provides remote state storage, team collaboration, and automated runs for Terraform.

**Q67) What is Terraform Enterprise?**  
**Ans:** It's the self-hosted, paid version of Terraform Cloud with extra features for large organizations.

**Q68) What is a Terraform registry?**  
**Ans:** An online library where you can find and share providers and modules. The public one is at registry.terraform.io.

**Q69) How do you use a module from the registry?**  
**Ans:** Reference it in a module block with its source:

```hcl
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "3.0.0"
}
```

**Q70) What is the source argument in a module?**  
**Ans:** It tells Terraform where to find the module code—could be a local path, Git repo, or registry.

**Q71) What is a root module?**  
**Ans:** The main directory where you run Terraform commands. It's the top-level module that may call other modules.

**Q72) What is a child module?**  
**Ans:** A module that is called by another module. It's nested inside the root or another parent module.

**Q73) How do you pass variables to a module?**  
**Ans:** You set them as arguments when calling the module:

```hcl
module "example" {
  source        = "./my-module"
  instance_type = "t2.micro"
}
```

**Q74) How do you get outputs from a module?**  
**Ans:** Use module.module_name.output_name. Example: module.vpc.vpc_id

**Q75) What is terraform state list?**  
**Ans:** It lists all resources currently tracked in your state file.

**Q76) What is terraform state show?**  
**Ans:** It shows detailed information about a specific resource in the state.

**Q77) What is terraform state rm?**  
**Ans:** It removes a resource from the state file without destroying it. Terraform will "forget" about it.

**Q78) What is terraform state mv?**  
**Ans:** It moves a resource from one state address to another. Useful when renaming or refactoring.

**Q79) What is sensitive data in Terraform?**  
**Ans:** Data like passwords or API keys that should be hidden. You can mark variables as sensitive = true to hide them in output.

**Q80) How do you mark a variable as sensitive?**  
**Ans:** Add sensitive = true to the variable definition:

```hcl
variable "password" {
  type      = string
  sensitive = true
}
```
