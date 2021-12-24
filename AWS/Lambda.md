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
