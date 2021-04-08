# Terraform Sample Skelton 
Sample Terraform file layout (Skelton) example. There are many ways to layout files in a practical application development using Terraform, however I want to provide an example file layout so that early Terraform learner can start quickly. 

**I welcome your feedback!**
# Layout
|  Path  |  Description  |
| ---- | ---- |
|  `terraform.tf`  |  Global Terraform setting.   |
|  `envs/`  |  Environment settings and main resource definition. Typically this path includes `dev` or `staging`.|
|  `envs/main.tf`  |  Main resource definition files.|
|  `envs/outputs.tf`  |  (Optional) If this resource is used by other resource, you may want to expose some values to the resources. |
|  `envs/backend.tf`  |  (Recommended) The backend storage to store State file. |
  `envs/provider.tf`  |  Terraform provider setting. |
|  `modules/`  |  (Optional) Modules directory. This directory is typically follow the same pattern with `envs` directory except `provider.tf` and `backend.tf`|

# Example usages for each files

## `main.tf`
In `main.tf` you can do whatever you want do. But in a *Terraformy* way, you can basically use `module` key word to combine, integrate your modules.

```HCL
module "module1" {
  source         = "../../modules/module1"
  variable = "FooBar"
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
If you are going to deploy some cloud services, such as AWS, GCP or Azure this file include cloud provider configuration. 

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
