### API Gateway
API Gateway supports stateless RESTful APIs and stateful WebSocket APIs.\
API Gateway features:
* Supports Websocket, RT streaming
* Versioning, e.g. `/v1, /v2`
* Handles multiple envs: dev, test, prod
* Can handle security: authentication + authorization
* Can create API keys and handle requests throttling
* Swagger/OpenAPI import to quickly define APIs
* Transform and validate requests and responses
* Cache API responses
* **Can terminate TLS and be configured for mutual TLS**

API type is selected upon creation:
* HTTP API (newer than REST)
* WebSocket
* REST API / REST API private

Endpoint types can be:
* Edge-optimized. For global clients. Requests are routed through CloudFront edge locations => improved latency. The Gateway itself stays in one region.
* Regional. For clients within same region. Is not integrated with CloudFront edge locations by def, but it can be added manually to have some improve  improvements as above.
* Private. Can be accessed only in the VPC using an interface VPC endpoint (ENI). Resource policy is used to define access.

Endpoint integration types:
* HTTP
* Lambda
* AWS Service
* VPC Link
* Mock

#### Caching
API Gateway caches responses from your endpoint for a specified time-to-live (TTL) period, in seconds.\
API Gateway then responds to the request by looking up the endpoint response from the cache instead of requesting your endpoint.\
The default TTL value for API caching is 300 seconds. The maximum TTL value is 3600 seconds.\
TTL=0 means caching is disabled.

#### Timeout
API Gateway has a default (and max) timeout of 29secs when calling a target.

#### Throttling
You can configure throttling and quotas for your APIs to help protect them from being overwhelmed by too many requests.\
When request submissions exceed the steady-state request rate and burst limits, API Gateway begins to throttle requests.\
Clients may receive `429 Too Many Requests` error responses at this point. 

#### Security
1. IAM Permissions
* Create an IAM policy authorization and attach to User / Role
* API Gateway verifies IAM permissions passed by the calling app
* Good to provide access within your own infra.
* Leverages `Sig v4` capability where IAM creds are in headers.

Use case: **to give permissions to users in your AWS acc.**

![API_Gateway_security1.png](files/API_Gateway_security1.png)
2. Lambda Authorizer (formerly Custom Authorizer)
* Uses a Lambda to validate a token in headers
* Option to cache a result
* Lambda must return and IAM policy for the user

Use case: **helps with 3rd party types of auth: OAuth, SAML**\
![API_Gateway_security2.png](files/API_Gateway_security2.png)

3. Cognito User Pools
* Cognito fully manages user lifecycle
* API Gateway verifies identity automatically from AWS Cognito
* No custom implementation required
* :exclamation: Cognito only helps with authentication, not authorization.

Use case: **Client manages his own user pool in Cognito. Can be backed up by Facebook, Google login etc. Authorization layer must be implemented in backend**

![API_Gateway_security3.png](files/API_Gateway_security3.png)
