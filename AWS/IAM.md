### IAM

#### Users and groups.

A user can have no group.\
A user can belong to several groups.\
A group can only contain users, not other groups.

#### Permissions and policies.

Users and groups can have assigned JSON documents called policies, e.g.

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

*Inline* policy is a policy attached directly to a user or a role.

Access keys. Access keys are used to set up a CLI access or an access via SDK.

* Access key id ~= username,
* Secret Access Key ~= password.

How to issue AWS CLI commands for a specific profile?

 ```
 aws ec2 describe-instances --profile user1
 ```

##### IAM Roles

Roles are used to grant permissions to services.\
Roles have attached policies.

#### Assuming IAM Roles

IAM roles allow you to delegate access to users or services that normally don't have access\
to your organization's AWS resources. IAM users or AWS services can assume a role to obtain\
temporary security credentials that can be used to make AWS API calls.\
Consequently, you don't have to share long-term credentials for access to a resource.\
Using IAM roles, it is possible to access cross-account resources.

##### IAM Access advisor. Credentials Report.

**IAM Access advisor** shows the service permissions granted to a user.\
**Credentials report** is a list of all users.\
Both can be used for permissions audit.

##### AWS Cloudshell

AWS Cloudshell is an AWS CLI running in a browser on a AWS Console site. It does not need credentials and refers to the
activated region. It also allows storing files in a working directory which persist even after session restart.

#### IAM Conditions

Can make IAM policies more granular, e.g. restrict by IP:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {},
      "Action": "*",
      "Resource": "*",
      "Condition": {
        "NotIpAddress": {
          "aws:SourceIp": [
            "192.0.2.0./24"
          ]
        }
      }
    }
  ]
}
```

Other conditions.\
Restrict the region the API calls are made to:

```json
{
  "Condition": {
    "StringEquals": {
      "aws:RequestedRegion": [
        "eu-central-1"
      ]
    }
  }
}
```

Restrict based on tags:

```json
{
  "Condition": {
    "StringEquals": {
      "ec2:ResourceTag/Project": "DataAnalytics",
      "aws:PrincipalTag/Department": "Data"
    }
  }
}
```

Force MFA:

```json
{
  "Condition": {
    "BoolIfExists": {
      "aws:MultiFactorAuthPresent": true
    }
  }
}
```

#### IAM for S3
S3 distinguishes two types of resources: a bucket itself and objects within 
```json
{
      "Action": "s3:ListBucket",
      "Resource": "arn:aws:s3:::achirkov.com"
      ----
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::achirkov.com/*"
}
```

#### IAM Roles vs Resource based policies
There are 2 ways of allowing access to S3 Bucket for example:
* By assuming a Role
* By defining Resource based policy on the bucket level\
![](files/IAM_roles_vs_resource_policies.png)

Differences:
* When a user assumes a role he gives up his original permissions. Therefore, after assuming a role her is not able to make calls to other resources if they are not listed in this role. 
* When a user uses Resource based policy he does not give up his original permissions

:exclamation: Resource based policies are applied to: S3, SQS, SNS

#### IAM Permissions Boundaries
Look like IAM Roles and define maximum permissions and IAM entity can get.\
IAM Permissions Boundaries are supported for users and roles (no groups).\
A user/role can get an IAM policy that exceeds IAM Permissions Boundaries, but it will not work.

IAM Permissions Boundaries can be used in combination with AWS Organizations SCP.

Use cases: 
* Allow developers to self assign policies and manage their own permissions while making sure they can't escalate their privileges.
* Delegate responsibilities to non admins within their Permissions Boundaries for example to create new users
* Restrict one specific user instead of whole acc using Organizations SCP. 

#### Policy evaluation logic
:exclamation: Explicit denies always win.\
Session policy if for STS\
![](/home/andreychirkov/Projects/refcards/AWS/files/PolicyEvaluationHorizontal111621.png)
