### Lambda
Includes:
* Lambda
* Dynamo
* Cognito
* API Gateway
* SNS, SQS, Kinesis
* Fargate
* Aurora Serverless

#### Lambda
Features:
* run on demand
* auto scaling
* short execution - <15min (timeout can be specified)
* easy pricing: per request and compute time
* integration with languages:
  * Node.js,
  * Python
  * Go
  * C#
  * Ruby
  * Java
  * Custom runtime API community supported, e.g. Rust
  * Lambda container image which must implement Lambda Runtime API
* easy monitoring through CloudWatch
* easy to scale resources per function (up to 10GB RAM). Increasing RAM also increases CPU and Network.

Free tier includes 1kk requests and 400k GB*sec of compute time (=400k seconds on 1GB RAM).

#### Access
Each Lambda has an IAM role

#### Lambda Limits
Execution:
* mem allocation 128MB - 10 GB
* max exec time 15 mins
* env vars - 4Kb
* Disk capacity in the function container (in `/tmp`) - 512 Mb
* concurrency execution - 1000

Deployment:
* Lambda fun deployment size compressed .zip - 50 Mb
* Size of uncompressed deployment (code + dependencies) - 250 Mb
* Can use `/tmp` dir for loading more files

#### Lambda at Edge
Background: there is a deployed CDN using CloudFront.\
How to implement request filtering before reaching the app?

For this Lambda@Edge can be used: deploy Lambda functions alongside your CLoudFront CDN.
Use cases:
* use Lambda to change CLoudFront requests and responses.
* Website security and privacy
* User tracking and analytics
* A/B testing

#### Lambda in VPC
Lambda functions always operate from an AWS-owned VPC.\
By default, your function has the full ability to make network requests to any public internet address — this includes access to any of the public AWS APIs.\
For example, your function can interact with AWS DynamoDB APIs to PutItem or Query for records.

You should only enable your functions for VPC access when you need to interact with a private resource located in a private subnet.\
An RDS instance is a good example.

Once your function is VPC-enabled, all network traffic from your function is subject to the routing rules of your VPC/Subnet.\
If your function needs to interact with a public resource, you will need a route through a NAT gateway in a public subnet.

#### Lambda Layers
You can configure your Lambda function to pull in additional code and content in the form of layers.\
A layer is a ZIP archive that contains libraries, a custom runtime, or other dependencies.\
With layers, you can use libraries in your function without needing to include them in your deployment package.\
Layers let you keep your deployment package small, which makes development easier. A function can use up to 5 layers at a time.

You can create layers, or use layers published by AWS and other AWS customers.\
Layers support resource-based policies for granting layer usage permissions to specific AWS accounts, AWS Organizations, or all accounts.\
The total unzipped size of the function and all layers can't exceed the unzipped deployment package size limit of 250 MB.
