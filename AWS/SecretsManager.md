### Secrets manager
Secrets Manager supersedes the SSM Parameter Store for storing secrets and appeared later. 

* Capability to force rotation of secrets every X days
* Automate generation of secretes on rotation (uses a separate Lambda)
* Integrated with RDS (**Mostly meant for RDS integration**)
* Encryption by KMS 
* For general type of secrets (non-RDS) stores secrets in the form of key-value pairs
