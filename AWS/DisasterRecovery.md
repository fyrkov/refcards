### Disaster Recovery 

**RPO - Recovery Point Objective** = how often do you run backups.\
Defines how much data loss is acceptable, e.g. <=1 hr if backups occur every hour. 

**RTO - Recovery Time Objective** = how long does it take to recover after a disaster.\
Defines downtime between disaster and recovery.

#### Disaster Recovery strategies:
* Backup and Restore
* Pilot Light - a small version of the app (critical core) is always running in the cloud
* Warm Standby - full system uo and running at minimum size
* Hot Site / Multi-Site Approach - an app copy at full scale running in background
