### ASG - Auto scaling groups
LBs and Auto scaling groups work well together, i.e. when ASG scales out the app, LB registers new instances.\
For that ASG needs to be attached during creation to ALB's TG or CLB.\
Then if ASG scales out, new instances get register in LB automatically.

ASG is configured with:
* min size, desired capacity, maximum size
* launch configuration/template: AMi + instance type, EC2 user data, EBS, SGs, SSH key pair
* scaling policies
* network + subnets info
* LB info

Scaling policy can work on `CloudWatch` alarms.\
Alarm can monitor metrics like avg CPU, # of requests per instance on the ELB, avg network in/out.\
A custom metric can be added in the app and pushed to CloudWatch.

Scaling policy can also work based on schedule - e.g. for predicted peak loads.

IAM role attached to an ASG is propagated to spawned EC2 instances.

ASG does not only scale out/in but also re-creates an instance if it goes down for whatever reason (extra safety).\
ASG can terminate instances that an ELB marks as unhealthy and then replace them.

ASGs have its own healthcheck directly on EC2s (by default) but can also optionally refer to ELB's healthcheck so that ASG and LB work in sync.

ASGs are free of charge.

#### Scaling policies
* Dynamic scaling. Metrics based. Metrics can be: CPU utilization, RequestCountPerTarget, avg network IO, any custom metrics pushed to CloudWatch.
  * Simple scaling. Takes action when CloudWatch alarm is triggered.
  * Step scaling. Same as simple scaling but with multiple steps.
  * Target tracking scaling. ASG tries to keep the target metric within the given range.
* Scheduled scaling
* Predictive scaling. Based on forecast built on top of historical data.

**Scaling cooldown** - 5 mins by default.\
Cooldown is activated after scaling activity happens.\
During this period ASG will not do any actions.

Advice: use ready-to-use AMIs to reduce configuration time (EC2 user data script, etc).


#### ASG Termination policies
1. Default. Choose the AZ with most instances. Delete the instance with the oldest launch configuration.
2. Lifecycle hooks. An ability ti perform extra steps upon scaling in/out. Instances go into aux `Pending` or `Terminating` states.\
This can help to gather logs for example from a crashing instance before it gets terminated.

#### Launch templates vs Launch configuration
LC is legacy. Must be re-created every time config changes.

LT is newer. Can have multiple versions.\
Have parameters subsets for re-use and inheritance.\
Provision both on-demand and Spot instances.
