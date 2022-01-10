### AWS Step Functions
AWS Step Functions lets you coordinate and orchestrate multiple AWS services such as AWS Lambda and AWS Glue into serverless workflows.\
Workflows are made up of a series of steps, with the output of one step acting as input into the next.\
A Step Function automatically triggers and tracks each step, and retries when there are errors, so your application executes in order and as expected.\
The Step Function can ensure that the Glue ETL job and the lambda functions execute in order and complete successfully as per the workflow defined.

#### Step Functions vs SWF (Simple Workflow Service)
Step Functions are 100% managed and serverless.\
SWF requires infra like EC2 instances, requires writing code.
