The `terraform {}` block configures Terraform itself, including which providers to install, and which version of Terraform to use to provision your infrastructure.

Terraform uses binary plugins called providers to manage your resources by calling your cloud provider's APIs.

After setting up a `main.tf` and `terraform.tf`:
- `terraform init` downloads the listed providers
- `terraform fmt` formats any .tf files
- `terraform validate` checks the configurations

## Creating Infrastructure
Terraform makes change to infrastructure in two steps:
- Terraform creates an execution plan for the changes it will make.  Review this plan to ensure that Terraform will make the changes you expect.
- Once you approve the execution plan, Terraform applies those changes using your workspace's providers.

You can then apply your configuration with the `terraform apply` command. When you use Terraform to plan and apply changes to your workspace's infrastructure, Terraform compares the last known state in your state file, your current configuration, and data returned by your providers to create its execution plan.

Your state file can include sensitive information, so you must store your state file securely and restrict access to only those who need to manage your infrastructure with Terraform.

## Managing Infrastructure

### Variables and Outputs
Input variables allow you to parametrize the behaviour of your Terraform configuration. You can also define output variables to expose data about the resources you create.

In variables.tf,
```
variable "instance_name" {
  description = "Value of the EC2 instance's Name tag."
  type        = string
  default     = "team-c-backend"
}

variable "instance_type" {
  description = "The EC2 instance's type."
  type        = string
  default     = "t2.micro"
}
```


Output values allow you to access attributes from your Terraform configuration and consume their values with other automation tools or workflows.

In outputs.tf

```
output "instance_hostname" {
  description   = "Private DNS name of the EC2 instance"
  value         = aws_instance.app_server.private_dns
}
```

### Module Blocks
Modules are reusable sets of configuration. Module are used to consistently manage complex infrastructure deployments that include multiple resources and data sources. Like providers, you can source modules from the Terraform Registry.

Module blocks can be added to create a VPC and related networking resources for your EC2 instance.

main.tf
```terraform
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "5.19.0"

  name = "example-vpc"
  cidr = "10.0.0.0/16"

  azs             = ["us-west-2a", "us-west-2b", "us-west-2c"]
  private_subnets = ["10.0.1.0/24", "10.0.2.0/24"]
  public_subnets  = ["10.0.101.0/24"]

  enable_dns_hostnames    = true
}
```

claude --resume e2950f75-239d-4833-9aaa-47236f4cceb8