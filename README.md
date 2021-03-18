# Terraform Sample Skeleton 
Sample Terraform skeleton for practical use

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