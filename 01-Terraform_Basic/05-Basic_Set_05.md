**Q81) What is the difference between Terraform and Ansible?**  
**Ans:** Terraform focuses on creating and managing infrastructure. Ansible focuses on configuring and managing software on that infrastructure. They often work together.

**Q82) What is the difference between Terraform and CloudFormation?**  
**Ans:** CloudFormation only works with AWS. Terraform works with multiple cloud providers and services.

**Q83) Can Terraform manage existing infrastructure?**  
**Ans:** Yes, using terraform import you can bring existing resources under Terraform management.

**Q84) What happens if you lose your state file?**  
**Ans:** Terraform won't know what resources it created. You'd need to import them again or risk duplicating resources.

**Q85) How do you backup the state file?**  
**Ans:** Use a remote backend like S3 with versioning enabled. Never rely only on the local file.

**Q86) What is drift in Terraform?**  
**Ans:** Drift happens when the real infrastructure changes outside of Terraform. Running plan or refresh can detect it.

**Q87) What are dynamic blocks?**  
**Ans:** They let you create repeating nested blocks inside a resource based on a variable. Useful for things like multiple ingress rules.

**Q88) What is the splat expression?**  
**Ans:** It's a shorthand to get an attribute from all items in a list. Example: aws_instance.example[*].id returns all instance IDs.

**Q89) What is the ternary operator in Terraform?**  
**Ans:** A conditional expression: condition ? true_value : false_value. Example: var.env == "prod" ? "m5.large" : "t2.micro"

**Q90) What is the coalesce function?**  
**Ans:** It returns the first non-null, non-empty value from a list of arguments.

**Q91) What is the try function?**  
**Ans:** It attempts to evaluate expressions and returns the first one that doesn't error.

**Q92) What is the can function?**  
**Ans:** It checks if an expression can be evaluated without errors, returning true or false.

**Q93) What are type constraints in variables?**  
**Ans:** You can specify what type a variable should be: string, number, bool, list, map, object, etc.

**Q94) What is an object type?**  
**Ans:** A complex type that has specific named attributes. Like a struct with defined fields.

**Q95) What is a tuple type?**  
**Ans:** A list where each element can be a different type. The types are defined in order.

**Q96) What is terraform providers command?**  
**Ans:** It shows the providers required by your configuration and their versions.

**Q97) What is terraform providers lock?**  
**Ans:** It creates or updates the dependency lock file (.terraform.lock.hcl) to pin exact provider versions.

**Q98) What is .terraform.lock.hcl?**  
**Ans:** A file that locks the exact versions of providers used in your project. It ensures everyone uses the same versions.

**Q99) Should you commit .terraform.lock.hcl to version control?**  
**Ans:** Yes! It ensures consistent provider versions across your team and CI/CD pipelines.

**Q100) What is the best way to learn Terraform?**  
**Ans:** Start with the official HashiCorp tutorials, practice building small projects, read the documentation, and gradually move to more complex setups. Hands-on practice is key!
