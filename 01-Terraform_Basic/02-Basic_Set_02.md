**Q21) What is terraform validate?**  
**Ans:** This command checks if your Terraform files have any syntax errors. It doesn't connect to any cloud—just checks your code.

**Q22) What is terraform fmt?**  
**Ans:** This command formats your Terraform files to look clean and consistent. It fixes indentation and spacing automatically.

**Q23) What is a data source in Terraform?**  
**Ans:** A data source lets you fetch information about existing resources that weren't created by your Terraform code.

**Q24) What is the difference between resource and data source?**  
**Ans:** A resource creates or manages something. A data source only reads information about something that already exists.

**Q25) What is terraform refresh?**  
**Ans:** This command updates the state file to match the real-world infrastructure. It checks what actually exists.

**Q26) Can Terraform work with multiple cloud providers?**  
**Ans:** Yes! You can use AWS, Azure, Google Cloud, and many others in the same Terraform project.

**Q27) What is a Terraform backend?**  
**Ans:** A backend defines where and how Terraform stores its state file. You can store it locally or remotely in places like S3 or Terraform Cloud.

**Q28) Why use a remote backend?**  
**Ans:** Remote backends let teams share the state file safely. They also provide locking so two people don't make changes at the same time.

**Q29) What is state locking?**  
**Ans:** State locking prevents multiple people from running Terraform at the same time. This stops conflicts and corruption.

**Q30) What is terraform show?**  
**Ans:** This command displays the current state or a saved plan in a readable format.

**Q31) What is terraform output?**  
**Ans:** This command prints the output values defined in your configuration.

**Q32) What is terraform import?**  
**Ans:** This command lets you bring existing infrastructure into Terraform's management. You're telling Terraform about resources that already exist.

**Q33) What is terraform taint?**  
**Ans:** This command marks a resource for recreation. The next time you apply, Terraform will destroy and recreate it. (Note: deprecated in newer versions, use -replace flag instead)

**Q34) What is terraform untaint?**  
**Ans:** This removes the taint mark from a resource, so Terraform won't recreate it.

**Q35) What is a provisioner in Terraform?**  
**Ans:** A provisioner runs scripts or commands on a resource after it's created, like installing software on a new server.

**Q36) What are the types of provisioners?**  
**Ans:** Common types are local-exec (runs on your machine) and remote-exec (runs on the created resource).

**Q37) Should you use provisioners often?**  
**Ans:** No, provisioners are a last resort. It's better to use tools like cloud-init, Ansible, or pre-built images when possible.

**Q38) What is the depends_on argument?**  
**Ans:** It tells Terraform that one resource must be created before another. Terraform usually figures this out automatically, but sometimes you need to specify it.

**Q39) What is the count argument?**  
**Ans:** Count lets you create multiple copies of a resource. For example, count = 3 creates three identical resources.

**Q40) What is for_each in Terraform?**  
**Ans:** Similar to count, but it creates resources based on a map or set. Each resource can have different values.
