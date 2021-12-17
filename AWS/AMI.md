### AMI

Custom user AMIs can be created from running EC2 instances with installed and configured dependencies.

AMIs are built for a specific AWS Region, they're unique for each AWS Region.\
It is not possible to launch an EC2 instance using an AMI in another AWS Region,
but it is possible to copy AMI to the target AWS Region and then use it to create your EC2 instances.
