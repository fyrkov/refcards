### Terraform

#### Main commands
* `terraform init` - initializing backend, pulling plugins, modules
* `terraform validate` - verify configuration is syntactically valid and internally consistent, regardless of any provided variables or existing state
* `terraform plan`
* `terraform apply`
* `terraform destroy`

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

#### Variables
Types of variables:
* string
* number: a numeric value (integer and fractional).
* bool
* list (or tuple): a sequence of values, like `["us-west-1a", "us-west-1c"]`. 
* map (or object): a group of values identified by named labels, like `{name = "Mabel", age = 12}`.

#### Authentication
1. Directly specifying access_key and secret_key in the `main.tf`
2. Env variables
3. Have AWS CLI and set up a profile with `aws configure`. Specify the profile in the `main.tf`

#### Tfenv
Terraform version manager.\
A specific version for a project can be specified in the `.terraform-version` in the root folder.\
Run `tfenv install` to set up a terraform distribution with the specified version.

#### variable.tfvars vs. variable.tf
What is the difference between `variable.tf` and `variable.tfvars` in Terraform?

* The `.tf` uses modules to **declare variables**.
* The `tfvars` file is in the root folder of an environment to define values to variables (as one of the ways).

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

#### Modules
Module is a set of Terraform files in a single directory.\
Modules can be remote.\
Official remote modules can be found in Terraform Registry: https://registry.terraform.io/browse/modules

Example, using aws VPC module instead of defining a VPC resource manually:
```
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "3.12.0"
  # insert the 23 required variables here
}
```

Terraform requires `init` to pull a remote module before using it.

Modules can call other modules. They are called child modules in such case.

#### Passing variables
1. Using env vars gives a secure method to provide sensitive data 
```
export TF_VAR_vpcname=myvpc
terraform plan
```

2. Using CLI vars
```
terraform plan -var="vpcname=myvpc"
```

3. Using `tfvars` file\
Create a `terraform.tfvars` file in the root folder:
```
vpcname = "myvpc"
```

4. Using multiple `tfvars` files\
Create a `prod.tfvars` file in the root folder:
```
vpcname = "prod_vpc"
```
Pass the filename in CLI:
```
terraform plan -var-file=prod.tfvars
```

Terraform loads variables in the following order, with later sources taking precedence over earlier ones:

* Environment variables
* The `terraform.tfvars` file, if present
* Any `*.auto.tfvars` files
* Any `-var` and `-var-file` options on the CLI

#### Advanced commands
* `terraform fmt [-recursive]` - formats files in a directory and optionally (with `-recursive`) in submodules
* `terraform taint` - informs Terraform that a particular object has become degraded or damaged.\
  Terraform represents this by marking the object as "tainted" in the Terraform state, and Terraform will propose to replace it in the next plan you create.\
  Superseded by the `terraform apply -replace="aws_instance.example[0]"` option.\
  Use `terraform untaint`  to remove the taint marker from the object.
* `terraform import aws_vpc.vpcimport <resource_id>` - imports an existing resource to the Terraform state.\
Can be useful when some resources are created in the AWS Console and need to be added under the Terraform control.\
Configuration must be updated for the resource before importing.

* `terraform workspace`\
Each Terraform configuration has an associated backend where Terraform state is stored.\
The persistent data stored in the backend belongs to a **workspace**.\
Initially the backend has only one "default" workspace.\
Certain backends support multiple named workspaces, allowing multiple states to be associated with a single configuration.\
The configuration still has only one backend, but multiple distinct instances of that configuration to be deployed without configuring a new backend or changing authentication credentials.
  * `terraform workspace list`
  * `terraform workspace new <name>` - create new workspace
  * `terraform workspace show` - show current workspace
  * `terraform workspace select default` - to change workspace
  * `terraform workspace delete <name>` - to delete workspace

* `terraform state` commands
  * `terraform show` - to provide human-readable output from a state
  * `terraform state list` - to list resources within a Terraform state (without attributes unlike `terraform show`)
  * `terraform state show <resource_reference>` - to show the attributes of a single resource in the Terraform state
  * `terraform state pull` - to see the state stored in a remote backend
  * `terraform state mv <curr_name> <new_name>` - to rename resources in the state
  * `terraform state rm <name>` - to delete resources in the state. May be useful if a resource is manually deleted in AWS Console.

* `terraform get` is used to download and update modules mentioned in the root module.
* `terraform console` provides an interactive console for evaluating expressions.\
  It will read the Terraform configuration in the current working directory and the Terraform state file from the configured backend.
* `terraform force-unlock [options] LOCK_ID [DIR]` manually unlock the state for the defined configuration.
* `terraform refresh` - reads the current settings from all managed remote objects and updates the Terraform state to match.\
  The command is a part of `plan` and `apply` steps. ~~Deprecated~~. Not deprecated again in TF v1.1.7?
* `terraform apply -refresh-only` - depending on version? see, `refresh`
* `terraform apply -refresh=true` - depending on version? see, `refresh`
* `terraform apply -replace=aws_instance.web` - manually marks a Terraform-managed resource for replacement, forcing it to be destroyed and recreated on the apply execution.
* `terraform login` - to automatically obtain and save an API token for Terraform Cloud
* `terraform graph` - to generate a visual representation of either a configuration or execution plan.\
The output is in the DOT format, which can be used by GraphViz to generate charts.
* `terraform init -reconfigure` - to update the backend configuration (e.g. local -> S3)
* `terraform init -backend-config=PATH` - to merge missing parts of the "partial" configuration with the provided file (e.g. secrets) 
* `terraform plan -out=FILE` -  write a plan file to the given path.\
This can be used as input to the "apply" command.

#### Debugging
Set Terraform logging mode with: 
```
export TF_LOG=TRACE
```
To persist logged output you can set `TF_LOG_PATH` in order to force the log to always be appended to a specific file when logging is enabled.

#### State. Backends
The local backend stores state on the local filesystem, locks that state using system APIs, and performs operations locally.

The remote backend is typically stored in S3 with enabled versioning and encryption.\
Create a `backend.tf`, example:
```hcl
terraform {
  backend "s3" {
    bucket = "bucket-name"
    key = "state/terraform.tfstate"
    region = "us-east-1"
  }
}
```

Only one backend is allowed.

A state file may contain sensitive information such as secrets.\
Hence it must be stored securely.

A state is protected with a lock to prevent concurrent updates.\
Not all backends support locking.


#### Handling Secrets
1. Do not store secretes in plain text: `.tf` files or other files.
2. Mark variables as sensitive if necessary.
```hcl
variable "x" {
  type      = string
  sensitive = true
}
```
They will still appear in state files!
3. Use environment variables to pass secrets that will not be stored in state files.
```
export TF_VAR_password=123
```
4. Use secret stores: Vault, AWS Secrets Manager, etc.
```hcl
provider "vault" {
  address = "http://127.0.0.1:8200"
}
data "vault_generic_secret" "db_pass" {
  path = "secret/app"
}
// could be a resource, but here it is data for testing purposes
output "db_pass" {
  value = data.vault_generic_secret.db_pass.data["db_password"]
  sensitive = true
}
```

#### Using structural data types
Structural data types: list, tuple, map, object
```hcl

```
