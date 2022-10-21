### AWS Backup
Managed service to automate backups across AWS services.\
No need to create custom scripts, no manual process.\
Backups are stores in S3.

Supports services:
* FSx
* EFS
* Dynamo
* EC2
* EBS
* RDS, Aurora
* Storage Gateway (Volume Gateway)

Supports also 
* Cross-region and cross-account backups.
* Tag-based backup policies
* On-demand and scheduled backups (e.g. tagged EC2 instance get backed up)
* Backup policies known as **Backup Plans** 
  * Backup frequency 
  * Backup window
  * Transition to Cols Storage (never, days, weeks...)
  * Retention period (never, days, weeks...)

After a backup plan is created it is possible to assign resources to it (=select services to backup) either by resources ids or by tags.
