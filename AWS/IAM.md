### IAM
Users and groups.\
A user can have no group.\
A user can belong to several groups.\
A group can only contain users, not other groups.

Permissions and policies.\
Users and groups can be assigned JSON documents called policies, e.g.
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow", // Allow or Deny
            "Principal": {} // refers to account, user or role
            "Action": [
                "dynamodb:Query",
                "dynamodb:Scan",
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}
```
*Inline* policy is a policy attached directly to a user.

Access keys.
Access keys are used to set up a CLI access or an access via SDK.
 * Access key id ~= username,
 * secret Access Key ~= password.


 How to issue AWS CLI commands for a specific profile?
 ```
 aws ec2 describe-instances --profile user1
 ```

IAM Roles\
Roles are used to grant permissions to services.\
Roles has attached policies.

 ##### IAM Access advisor. Credentials Report.
 IAM Access advisor shows the service permissions granted to a user.
 Credentials report is a list of all users.
 Both can be used for permissions audit.

 ##### AWS Cloudshell
 AWS Cloudshell is an AWS CLI running in a browser on a AWS Console site. It does not need credentials and refers to the activated region.
 It also allows storing files in a working directory which persist even after session restart.
