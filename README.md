# Terraform Sample Skeleton 
Sample Terraform file layout (Skeleton) example. There are many ways to layout files in a practical application 
development using Terraform, however I want to provide an example file layout so that early Terraform 
learner can start quickly. 

_Modularize_ is a holy grail for any type of software development. In Terraform, it's also applied. 
This skeleton may give you an idea to lay out the files meaningfully to follow to modularize your Terraform code.

**I welcome your feedback!**
# Layout
|  Path  |  Description  |
| ---- | ---- |
|  `envs/`  |  Environment settings and main resource definition. Typically this path includes `dev` or `staging`.|
|  `envs/<env_name>/main.tf`  |  Main resource definition files.|
|  `envs/<env_name>/outputs.tf`  |  (Optional) If this resource is used by other resource, you may want to expose some values to the resources. |
|  `envs/<env_name>/backend.tf`  |  (Recommended) The backend storage to store State file. |
|  `envs/<env_name>/versions.tf`  |  (Recommended) Specify the terraform and provider versions. |
|  `envs/<env_name>/local.tf`  |  (Recommended) Specify local variables. |
  `envs/<env_name>/provider.tf`  |  Terraform provider setting. |
|  `modules/`  |  (Optional) Modules directory. This directory is typically follow the same pattern with `envs` directory except `provider.tf` and `backend.tf`|

# Example usages for each files

## `main.tf`
In `main.tf` you can do whatever you want to do. But in a *Terraformy* way, you can basically use `module` key word to combine, integrate your modules.

```HCL
module "module1" {
  source         = "../../modules/module1"
  variable = "FooBar"
}
```

You may also want to take the output from a module to pass another module as parameters. Here's the simple sample.

- Example


```HCL
module "module1" {
  source         = "../../modules/module1"
  variable = "FooBar"
}

module "module2" {
  source         = "../../modules/module2"
  variable = module.module1.some_output
}
```

The syntax to take outputs from modules are, 

_module_.**<your_name_of_module>**.**<output_name>**

In this case, you need to define the output data in `outputs.tf` in `modules/module1`,

`modules/module1/outputs.tf`
```HCL
output "some_output" {
  value   = aws_security_group.my_sg
}
```

## `terraform.tf`
Usually this file contains Terraform version restriction, so that you can deploy your applications with Terraform version consistency. 

- Example   
If you want to ensure the Terraform version upper than `0.14.7`

```HCL
terraform {
  required_version = ">= 0.14.7"
}
```
## `provider.tf`
If you are going to deploy some cloud services, such as AWS, GCP or Azure this file includes cloud provider configuration. 

- Example 1  
For provider for AWS, it is commonly used for region setting. 

```HCL
provider "aws" {
  region = "us-east-1"
}
```

- Example 2  
Sometimes you want to deploy your resources in different regions, you can use this file to define the provider setting with `alias`.

```HCL
provider "aws" {
  region = "us-east-1"
}

provider "aws" {
  region = "us-west-2"
  alias = "oregon"
}
```

## `backend.tf`
It is critically important to manage, store your Terraform state file in secure, durable way. The most common way (I believe) to do this is store them in Cloud Storage, such as Amazon S3. 

- Example  
For S3 backend, you can store your Terraform state file in the S3 bucket named `my-terraform-backend`. 

```HCL
terraform {
  backend "s3" {
    bucket = "my-terraform-backend"
    key    = "dev/terraform.tfstate"
    region = "us-east-1"
  }
}
```

## `outputs.tf`
You may want to pass your resource outputs to other resources. You can define your output values in this file.

```HCL
output "instance_ip_addr" {
  value = aws_instance.server.private_ip
}
```

## `version.tf`
It is the best practice to specify the terraform and provider versions. It prevents your code from breaking change on versions. 

```HCL
terraform {
  required_version = ">= <YOUR TERRAFORM VERSION>"
  required_providers {
    aws = "<PROVIDER VERSION>"
  }
}
```

## `local.tf`
`locals` specifies the local variable processing. it is the best practice to locate all local variable into a file. 
