### ASG - Auto scaling groups
ELBs and Auto scaling groups work well together, i.e. when ASG scales out the app, ELB registers new instances.\
For that ASG needs to be attached during creation to ALB's TG or CLB.\
Then if ASG scales out, new instances get registered in LB automatically.

ASG is configured with:
* min size, desired capacity, maximum size
* launch configuration/template: AMi + instance type, EC2 user data, EBS, SGs, SSH key pair
* scaling policies
* network + subnets info
* LB info

Scaling policy can work on top of `CloudWatch` alarms.\
An alarm can monitor metrics like avg CPU, # of requests per instance on the ELB, avg network in/out.\
A custom metric can also be added in the app and pushed to CloudWatch.

Scaling policy can also work based on schedule - e.g. for predicted peak loads.

IAM role attached to an ASG is propagated to spawned EC2 instances.

ASG does not only scale out/in but also re-creates an instance if it goes down for whatever reason (extra safety).\
ASG can terminate instances that an ELB marks as unhealthy and then ASG replaces them.

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
Cooldown is activated after a scaling activity happens.\
During this period ASG will not perform any actions.

Advice: use ready-to-use AMIs to reduce configuration time (EC2 user data script, etc).

:exclamation: When there are multiple policies in force at the same time, there's a chance that each policy could instruct ASG to scale out (or in) at the same time. Then ASG chooses the policy that provides the largest capacity for both scale-out and scale-in.

#### ASG lifecycle hooks
Lifecycle hooks enable you to perform custom actions as the ASG launches or terminates instances.\
When an instance is paused, it remains in a wait state either until you complete the lifecycle\
action using the `complete-lifecycle-action` command or the `CompleteLifecycleAction` operation,\
or until the timeout period ends (one hour by default).

For example, you could install or configure software on newly launched instances,\
or download log files from an instance before it terminates.

#### ASG Termination policies
1. Default. Choose the AZ with most instances. Delete the instance with the oldest launch configuration.
2. Lifecycle hooks. An ability to perform extra steps upon scaling in/out. Instances go into aux `Pending` or `Terminating` states.\
This can help for example to gather logs from a crashing instance before it gets terminated.

#### Launch templates vs Launch configuration
LC is legacy. Must be re-created every time config changes.

LT is newer. Can have multiple versions.\
Have parameters subsets for re-use and inheritance.\
Provision both on-demand and Spot instances.

#### Multi-AZ
ASG can work across multiple AZs in an AWS Region.\
ASG can't contain EC2 instances from multiple regions.

#### Instance tenancy in VPC
By default, all instances in the VPC run as shared tenancy instances.\
When you create a launch configuration, the default value for the instance placement tenancy is `null` and the instance tenancy is controlled by the tenancy attribute of the VPC.\
You can specify the instance placement tenancy for your launch configuration as `default` or `dedicated` and thus override VPC setting.
