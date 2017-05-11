# Auto Scaling

## Basics
- Auto Scaling is a service that allows scaling of EC2 capacity automatically according to set criteria

## Plans
- Maintain Current Instance Levels
  - Keeps a minimum or specified number of instances at all times
  - Uses periodic health checks on running instances and launches a replacement one when an instance is unhealthy
- Manual Scaling
  - Most basic way to scale
  - Only need to specify the change in max, min, or desired capacity of the Auto Scaling Group, Auto Scaling service does the rest
- Scheduled Scaling
  - Useful for when needs are on a predictable schedule
  - Scaling is automatic as a function of time and date
- Dynamic Scaling
  - Allows the setting of defined parameters that control the scaling process
  - Example:  Adding more webservers when CloudWatch sends an alarm

## Auto Scaling Components
- Launch Configuration
  - Template that Auto Scaling uses to create new instances and is composed of the
    - Configuration Name
    - AMI
    - EC2 instance type
    - security Group
    - Instance Key pair
  - Security groups for instances launched in EC2-Classic may be reference by security group names or IDs; for VPC (recommended), the security group can only be referenced by the ID
  - The default limit for launch configurations is 100 per region
  - The default limit for EC2 instances in a region is 20
- Auto Scaling Group
  - Collection of EC2 instances managed by the Auto Scaling service
  - Each group contains configuration options that control when Auto Scaling should launch new instances and terminate existing ones
  - An Auto Scaling group must contain
    - A name
    - A minimum number of instances that can be in the group
    - Optionally
      - Desired Capacity (number of instances the group must have at all times)
      - If not specified, it is the specified minimum  number
  - On-Demand instances are default for Auto Scaling (Spot instances are also available with a specified Spot Price)
  - A configuration can use *either* On-Demand or Spot instances, not both
- Scaling policy
  - CloudWatch Alarms and scaling policies can be used with an Auto Scaling group to adjust scaling dynamically
  - Ways to configure a scaling policy
    - Increase or decrease a specific number of instances
    - Target number of instances
    - Adjust based on a percentage
  - More than one scaling policy can be with a scaling group
  - Recommended best practice is to scale out quickly and scale in slowly, to avoid destroying instances too quickly (and being charged full hours for partial uses, especially if instances have to be re-launched)
  - A cooldown period is configurable, it determines when to suspend scaling activities
