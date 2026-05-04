**Q1) What is Terraform?**  
**Ans:** Terraform is an open-source tool made by HashiCorp that lets you create, change, and manage your cloud infrastructure using code. Instead of clicking buttons in a cloud console, you write simple configuration files.

**Q2) What is Infrastructure as Code (IaC)?**  
**Ans:** Infrastructure as Code means managing your servers, networks, and other infrastructure by writing code files instead of doing it manually. This makes your setup repeatable and easy to track.

**Q3) What language does Terraform use?**  
**Ans:** Terraform uses HashiCorp Configuration Language (HCL). It's designed to be easy to read and write, even for beginners.

**Q4) What is a Terraform provider?**  
**Ans:** A provider is a plugin that tells Terraform how to talk to a specific cloud or service like AWS, Azure, Google Cloud, or even GitHub.

**Q5) What is a Terraform resource?**  
**Ans:** A resource is something you want to create or manage, like a virtual machine, database, or storage bucket. You define resources in your Terraform code.

**Q6) What is the terraform init command?**  
**Ans:** This command sets up your working directory. It downloads the required providers and prepares everything so Terraform can run.

**Q7) What is the terraform plan command?**  
**Ans:** This command shows you what Terraform will do before it actually does it. It's like a preview of the changes.

**Q8) What is the terraform apply command?**  
**Ans:** This command actually creates or updates your infrastructure based on your configuration files.

**Q9) What is the terraform destroy command?**  
**Ans:** This command deletes all the resources that Terraform created. Use it carefully!

**Q10) What is a Terraform state file?**  
**Ans:** The state file keeps track of what resources Terraform has created. It's usually named terraform.tfstate and helps Terraform know what exists and what needs to change.

**Q11) Why is the state file important?**  
**Ans:** Without the state file, Terraform wouldn't know what infrastructure already exists. It uses this file to compare your code with the real world.

**Q12) What is terraform.tfvars file?**  
**Ans:** This file stores variable values. Instead of typing values every time, you put them in this file and Terraform reads them automatically.

**Q13) What is a Terraform module?**  
**Ans:** A module is a container for multiple resources that are used together. It's like a reusable template that you can use in different projects.

**Q14) What is the difference between terraform plan and terraform apply?**  
**Ans:** terraform plan only shows what will happen. terraform apply actually makes those changes happen.

**Q15) What file extension does Terraform use?**  
**Ans:** Terraform files use the .tf extension.

**Q16) What is a Terraform variable?**  
**Ans:** A variable lets you pass values into your Terraform code. This makes your code flexible and reusable.

**Q17) How do you define a variable in Terraform?**  
**Ans:** You use a variable block like this:

```hcl
variable "instance_type" {
  default = "t2.micro"
}
```

**Q18) How do you use a variable in Terraform?**  
**Ans:** You reference it using var.variable_name. For example: var.instance_type

**Q19) What is an output in Terraform?**  
**Ans:** An output lets you display useful information after Terraform runs, like an IP address or resource ID.

**Q20) How do you define an output?**  
**Ans:** You use an output block:

```hcl
output "instance_ip" {
  value = aws_instance.example.public_ip
}
```
