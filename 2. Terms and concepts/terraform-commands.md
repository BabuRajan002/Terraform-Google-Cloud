# Terraform commands

* `terraform init` - Initialize the provider with the plugin
* `terraform plan` - preview the resources that will be created after terraform apply. Compares the current configuration with the exisiting state and builds execution plan which really requires the changes.
* `terraform apply` - create the infra resources
* `terraform destroy` - destroy the infra resources
* `terraform fmt` - auto format to match canonical conventions

## Terraform validator

* It comes between terrafom plan and apply phase. Runs the pre-deployment checks against organizations policies
