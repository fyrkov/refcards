### CloudFormation

AWS CloudFormation gives an easy way to create a collection of related AWS and third-party resources, and provision and manage them in an orderly and predictable fashion.

Use cases:
* Simplify infrastructure provision with *CloudFormation templates*
* Quickly replicate infrastructure, e.g. in another region
* Version control and tracking changes for the infrastructure

#### Beanstalk VS CloudFormation
Behind the scenes, Elastic Beanstalk uses CloudFormation to create and maintain resources.

#### Key concepts
* **template** is a JSON or YAML declarative code file that describes the intended state of all the resources you need to deploy your application.\
Template has components:
  * **Resources** - AWS resources
  * **Parameters** - dynamic inputs for template
  * **Outputs** - references to what have been created
  * Mappings
  * Conditionals - conditions to perform resources creation
  * Metadata
* **stack** implements and manages the group of resources outlined in your template, and allows the state and dependencies of those resources to be managed together.
* **change set** is a preview of changes that will be executed by stack operations to create, update, or remove resources.
* **stack set** is a group of stacks you manage together across multiple accounts or regions.

A **stack** cannot be used to deploy the same template across AWS accounts and regions.\
A **stack set** lets you create stacks in AWS accounts across regions by using a single AWS CloudFormation template.

#### Terraform VS CloudFormation
CloudFormation supports Terraform

#### Template Designer
A GUI tool in the console to visualize, create or modify JSON/YAMLS templates.
