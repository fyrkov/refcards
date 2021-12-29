### AWS Directory Service

#### AWS Managed Microsoft AD
* Create own AD in AWS, manage users locally, supports MFA
* Exists in-sync with on-premise AD. Users can authenticate against both.
* Establish "trust" connection with your on-premise AD

#### AD Connector 
* A **proxy** to redirect to on-premise AD
* Users are managed on the on-premise AD

#### Simple AD
* AD-compatible managed directory on AWS. Backed by Linux-Samba AD.
* Can not be joined with on-premise AD
