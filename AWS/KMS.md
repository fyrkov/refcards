### KMS Key Management Service
Is used in background by:
* EBS
* S3, server-side encryption
* Redshift
* RDS
* SSM 
* KMS also can mbe used with CLI and SDKs.

KMS uses:
* symmetric encryption with AES-256.
* asymmetric (RSA & ECC key pairs). Use case: 
  * encryption outside of AWS by users who can't call the KMS API.
  * Also, for sign/verify operations

KMS is able to manage keys and policies:
* create
* rotate policies
* disable/enable
* audit (using CloudTrail)

Three types of CMK (Customer Master Keys):
* **AWS managed keys**. Default, free. Naming starts with `aws/{service_name}`
* **Customer Managed Keys**. User keys created in KMS: 1$/month. 
* User keys imported (must be 256-bit symmetric key): 1$/month

Plus, there is pay for API call to KMS: 0.03$/1k calls.

#### Access 
To give access to someone:
* make sure the Key Policy allows the user
* make sure the IAM Policy allows the API calls

Key policies are similar to S3 Bucket policies.\
With **default KMS key policy** is very permissive:
* Created if you dont provide a specific KMS Key Policy
* Complete access to the key to the user account = entire AWS account. :exclamation: By allowing to root user we allow to all users and roles in this account.
* In this case, it is only necessary to give a user the right IAM policy

With **custom KMS key policy**:
* Explicitly define users, roles that can access the KMS key
* Define who can administer the key
* Useful for cross-account access of your KMS key

#### Copying snapshots across regions
KMS keys are bound to a specific regions, when you copy EBS snapshots between regions, they have to be re-encrypted. 

#### KMS Key Rotation
Automatic key rotation can be enabled for customer-managed CMS (not AWS managed).\
If enabled, auto key rotation happens every **1 year**.\
Previous key is kept active, so you can decrypt old data.\
New key has the same CMK ID (only backing key changes).

It is also possible to rotate manually, e.g. every **90 days, 180 days**, etc.\
:exclamation: New key has a different CMK ID.\
Keeps the previous key active, so you can decrypt old data.\
In this case better to use aliases (to hide the change of the key from the applications).\
Use case: good for rotations of keys that are not eligible for automatic rotation (like asymmetric keys).

#### CLI examples
```shell
# 1) encryption
aws kms encrypt --key-id alias/tutorial --plaintext fileb://ExampleSecretFile.txt --output text --query CiphertextBlob  --region eu-west-2 > ExampleSecretFileEncrypted.base64

# base64 decode
cat ExampleSecretFileEncrypted.base64 | base64 --decode > ExampleSecretFileEncrypted
```
```shell
# 2) decryption

# AWS already knows what key it needs to use because the info is included in blob of encrypted data
aws kms decrypt --ciphertext-blob fileb://ExampleSecretFileEncrypted  --output text --query Plaintext > ExampleFileDecrypted.base64  --region eu-west-2

# base64 decode
cat ExampleFileDecrypted.base64 | base64 --decode > ExampleFileDecrypted.txt
```
