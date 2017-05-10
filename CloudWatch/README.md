# CloudWatch

## Basics
- Monitors AWS resources and applications in real time
- Can collect metrics, create alarms/notifications, and make chances to resources based on defined rules
- CloudWatch supports actions such as SNS and AutoScaling
- Types of monitoring
  - Basic - Free, sends data points to CloudWatch every 5 minutes for a limited number of preselected metrics
  - Detailed - Paid, sends data every minute and allows data aggregation
  - Basic is the default
- Supports monitoring and specific metrics fro AWS cloud services, including
  - Auto Scaling
  - CloudFront
  - CloudSearch
  - DynamoDB
  - EC2
  - ECS
  - ElastiCache
  - EBS
  - ELB
  - EMR
  - and more
- CloudWatch metrics are received via a `GET` request
- Custom metrics can be defined
- CloudWatch has an API that supports `PUT` for getting metrics into CloudWatch as name-value pairs
- CloudWatch Logs Agent provides an automated way to send log data to CloudWatch Logs for EC2 instances running Amazon Linux or Ubuntu
- Limits of CloudWatch
  - 5000 alarms per AWS account
  - Metrics data is retained for two weeks
    - Longer storage can be achieved by using S3 or Glacier
