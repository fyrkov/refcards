### Terraform


#### Structure
A **module** is a collection of `.tf` files kept together in a directory.\
Nested directories are treated as completely separate modules, and are not automatically included in the configuration.

Blocks can declare:
* Provider - plugins to interact with cloud providers
* Resource - describes one or more infrastructure objects
* Data - for querying and getting info about infra objects back
* Variable - like function arguments
* Output - make info about infra available on the cli and for other TF configurations to use
* Module - containers for multiple resources to reuse in configurations

#### Authentication
1. Directly specifying access_key and secret_key in the `main.tf`
2. Env variables
3. Have AWS ClI and set up a profile with `aws configure`. Specify the profile in the `main.tf`

#### Tfenv
Terraform version manager.\
A specific version for a project can be specified in the `.terraform-version` in the root folder.\
Run `tfenv install` to set up a terraform distribution with the specified version.

#### variable.tfvars vs. variable.tf
So, what is the difference between `variable.tf` and `variable.tfvars` in Terraform?

The `tfvars` file is in the root folder of an environment to define values to variables (as one of the ways).\
The `.tf` uses modules to declare variables.

#### Provisioners
Provisioners are used to execute scripts on a local or remote machine as part of resource creation or destruction.\
Provisioners can be used to bootstrap a resource, cleanup before destroy, run configuration management, etc.\
Provisioners are needed because there are certain behaviors that can't be directly represented in Terraform's declarative model.

Example:
```shell
resource "aws_instance" "cls_instance_{{ cls_ix }}" {
...
  # We need to apply tags to the root_block_device here as there is no
  # other way to tag it
  provisioner "local-exec" {
    command = "aws --region {{ region }} ec2 create-tags --resources ${self.root_block_device.0.volume_id} --tags Key=SystemID,Value=card-linking-service Key=Team,Value=spring Key=Service,Value=card-linking-service Key=Name,Value={{ "cls%02d_device0" | format(cls_ix) }}"
  }
}
```
