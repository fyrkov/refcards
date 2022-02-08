### Terraform

#### Authentication
1. Directly specifying access_key and secret_key in the `main.tf`
2. Env variables
3. Have AWS ClI and set up a profile with `aws configure`. Specify the profile in the `main.tf`

#### Input vars

#### Tfenv
Terraform version manager.\
A specific version for a project can be specified in the `.terraform-version` in the root folder.\
Run `tfenv install` to set up a terraform distribution with the specified version.

#### variable.tfvars vs. variable.tf
So, what is the difference between `variable.tf` and `variable.tfvars` in Terraform?\

The `tfvars` file is in the root folder of an environment to define values to variables (as one of the ways).\
The `.tf` uses modules to declare variables.
