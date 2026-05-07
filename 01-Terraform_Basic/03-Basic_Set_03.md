**Q41) What is the difference between count and for_each?**  
**Ans:** Count uses numbers (0, 1, 2...) while for_each uses keys from a map or set, giving you more control.
**Example:**
```
resource "aws_instance" "example" {
 for_each = {
   dev1 = { ami = "ami-12345678", instance_type = "t2.micro" }
   dev2 = { ami = "ami-87654321", instance_type = "t3.micro" }
 }
 ami = each.value.ami
 instance_type = each.value.instance_type
 tags = {
   Name = each.key
 }
}
```
```
resource "aws_instance" "example" {
 count = 3
 instance_type = "t2.micro"
 ami = "ami-12345678"
 tags = {
   Name = "example-${count.index}"
 }
}
```

**Q42) What is a local value in Terraform?**  
**Ans:** Locals are like temporary variables inside your configuration. They help simplify complex expressions.

**Q43) How do you define a local value?**  
**Ans:** You use a locals block:

```hcl
locals {
  environment = "production"
}
```

**Q44) What is the lifecycle block?**  
**Ans:** It controls how Terraform creates, updates, or deletes resources. You can prevent deletion or create new resources before destroying old ones.

**Q45) What does prevent_destroy do?**  
**Ans:** It stops Terraform from deleting a resource. Useful for protecting important resources like databases.

**Q46) What does create_before_destroy do?**  
**Ans:** It tells Terraform to create the new resource first, then delete the old one. This helps avoid downtime.

**Q47) What is ignore_changes used for?**  
**Ans:** It tells Terraform to ignore certain attributes when checking for changes. Useful when something changes outside of Terraform.

**Q48) What is a Terraform workspace?**  
**Ans:** Workspaces let you have separate state files for different environments (like dev, staging, prod) using the same code.

**Q49) How do you create a new workspace?**  
**Ans:** Use the command: terraform workspace new dev

**Q50) How do you switch between workspaces?**  
**Ans:** Use the command: terraform workspace select dev

**Q51) What is the default workspace called?**  
**Ans:** The default workspace is simply named default.

**Q52) What is terraform console?**  
**Ans:** It opens an interactive console where you can test expressions and functions without applying changes.

**Q53) What is terraform graph?**  
**Ans:** It generates a visual representation of your resources and their dependencies in DOT format.

**Q54) What are Terraform functions?**  
**Ans:** Built-in functions that help you manipulate data, like join(), split(), length(), upper(), and many more.

**Q55) What is the join function?**  
**Ans:** It combines a list of strings into one string with a separator. Example: join("-", ["a", "b", "c"]) returns "a-b-c".

**Q56) What is the split function?**  
**Ans:** It breaks a string into a list. Example: split(",", "a,b,c") returns ["a", "b", "c"].

**Q57) What is the length function?**  
**Ans:** It returns the number of items in a list or characters in a string.

**Q58) What is the lookup function?**  
**Ans:** It finds a value in a map by key. Example: lookup({a = 1, b = 2}, "a", 0) returns 1.

**Q59) What is the file function?**  
**Ans:** It reads the contents of a file. Example: file("script.sh") returns the content of that file.

**Q60) What is string interpolation?**  
**Ans:** It means inserting variable values into strings using ${}. Example: "Hello, ${var.name}".
