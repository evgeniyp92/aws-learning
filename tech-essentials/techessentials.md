# AWS Tech Essentials

## Intro

### Benefits of Cloud Computing

- Pay as you go
- Benefit from economies of scale
- Capacity on demand
- Increase speed and agility
- Realize cost savings
- Global in minutes

### AWS Global Infrastructure

AWS has multiple availability zones and applications can be deployed to several availability zones for redundancy, optimization, etc

AZ's are clusters of Data Centers with redundant power, networking, connectivity

AZ's can themselves be clustered

### Region considerations

- Compliance (i.e. GDPR)
- Latency (closer to users = better)
- Price (regional differences)
- Serviceability (new services aren't available in all regions right away)
- Also factor edge locations and edge caching (CloudFront)

### Interacting with AWS

Every AWS service has an API, and the AWS Management Console is a GUI that interacts with the API

AWS CLI and AWS SDK are also available

AWS CLI:

```json5
// aws s3api list-buckets
{
    "Owner": {
        "DisplayName": "tech-essentials", 
        "ID": "d9881f40b83adh2896eb276f44ffch53677faec805422c83dfk60cc335a7da92"
    }, 
    "Buckets": [
        {
            "CreationDate": "2023-01-10T15:50:20.000Z", 
            "Name": "aws-tech-essentials"
        }, 
        {
            "CreationDate": "2023-01-10T16:04:15.000Z", 
            "Name": "aws-tech-essentials-employee-directory-app"
        } 
    ]
}
```

AWS SDK:

```python
import boto3
ec2 = boto3.client('ec2')
response = ec2.describe_instances()
print(response)
```

Benefits of using the CLI or SDK include automation, scripting, and programmatic access to AWS services

### Security and the Shared Responsibility Model

As the customer on AWS, you're responsible for 
- your data 
- identity and access management
- os, firewall, network configuration
- client-side data encryption
- network traffic protection
- server-side encryption

everything else is AWS's responsibility

### Protecting the root user

Root users have two sets of credentials: email and password, and root user access keys

Access keys consist of

- Access Key ID
- Secret Access Key

Do not create a root user access key unless you absolutely have to

#### Security best practices

- Choose a strong password for root
- Use MFA
- Never share the password or access keys
- Disable or delete root access keys
- Create IAM users for admin and everyday tasks

### AWS IAM

IAM consists of Authentication and Authorization

Authentication is the process of verifying the identity of a user, device, or application

Authorization is the process of determining what permissions an authenticated user has

IAM is global, and not tied to a region

Least privilege is a best practice in IAM, as well as users having no inherent permissions, and the permissions 
being group-based

```json
{
    "Version": "2012-10-17",
    "Statement": [{
        "Effect": "Allow",
        "Action": "*",
        "Resource": "*"
  }]
}
```

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyS3AccessOutsideMyBoundary",
      "Effect": "Deny",
      "Action": [
        "s3:*"
      ],
      "Resource": "*",
      "Condition": {
        "StringNotEquals": {
          "aws:ResourceAccount": [
            "222222222222"
          ]
        }
      }
    }
  ]
}
```

### IAM Roles

Roles can be used by someone or something to assume the permissions of the role

IAM roles do not have login credentials, are assumed programmatically, are temporary in nature, and are rotated when 
the ephemeral credentials expire

External identity providers can be used to assume roles, such as Google, Facebook, or Amazon -- these are federated 
users

### NB

EC2 is Virtual Machines on demand in the cloud

## AWS Compute

All services that can be set up to handle HTTP requests or other kinds of API requests are available under the Compute tab in AWS

These include:

- App Runner
- Batch
- EC2
- EBS
- Lambda
- Lightsail
- AWS Outposts
- SimSpace Weaver

Generally, all options can be broken down into VM's (instances), Containers, and Serverless

### Considerations for VM vs Container vs Serverless

EC2 is basically VM's on AWS hardware

### EC2

Must specify an Amazon Machine Image, which can be used to launch identical EC2 instances. Can also use community AMI's or build your own.

Amazon Linux is an AWS built Linux Image supported by AWS

You can configure instance types to pick the hardware your EC2 instance runs on

The aws template is `[instance type].[instance size]`

Instance size determines capacity, and instance type determines blend

Instance type further breaks down into `[instance family letter][instance generation number][additional features as letters]`

Instance sizes and types can be changed via API

EC2 is flexible and low cost and allows devs to innovate quickly and cheaply

AWS also provides help with Right Sizing

### EC2 Instance Lifecycle

EC2 is pay-as-you-use

1. Launch instance from AMI
  - Instance goes into pending while it is booting
2. Instance goes to running
  - Billing starts here
  - Can reboot, shutdown, stop [EBS only], stop-hibernate [EBS only].
3. Shutdown, stop and stop-hibernate states let you Terminate
  - Terminate throws away everything forever
  - Can be guarded against with Termination Protection
  - Persistent storage can retain data even if EC2 instances are thrown away
  - Terminating isnt a bad thing, may be necessary for patching, updates, etc

EC2 instances are metered while running and preparing to hibernate
EBS for EC2 volumes is metered at all times however

#### Pricing

Categories of EC2 Pricing

1. **On-Demand**
  - No commitment, no upfront, pay per hour or per second while instance is running
    - Good for ppl who want low cost and flexibility
    - Good for apps with unpredictable workloads that can't be interrupted
    - First-time devs and testers
2. **Spot**
  - Use spare EC2 capacity at a set desired price for potential savings
    - Good for apps with flexible start and end times
    - Feasible only at low compute prices
    - Fault-tolerant or stateless workloads
3. **Savings**
  - Low usage prices for a commitment
    - Good for customers with consistent and steady usage
    - Different instance types and compute solutions in different locations
    - Customers who can commit to EC2 for 1 or 3 years
4. **Reserved Instance**
  - Reserved capacity, with commitment and potential upfront
    - Different types: Standard, Convertible, Scheduled
5. **Dedicated Host**
  - A physical EC2 server of your own
    - Saves money with existing server-bound licenses like WServer, SQL Server, etc
    - Can be purchased on demand or reserved in advance for up to 70% off

### Containers

Container Orchestration options

- Amazon Elastic Container Service
- Amazon Elastic K8s Service

AWS Fargate is serverless compute for ECS and EKS

### Serverless

Serverless is nice because of horizontal scaling but also has inherent overheads, its important to consider convenience and resiliency vs operational costs

EC2, Lambda, Fargate, Elastic Beanstalk, EKS, all exist on a spectrum of `Control<---->Convenience`

#### Fargate

Fargate lets you run containers on a managed (on your behalf) cluster with ECS or EKS

1. build containers and push them to ECR
2. define memory and compute resources for ECS, or your pod for EKS
3. pay only for memory, compute and storage consumed

Good for common container use cases like microservices, batch processing, etc

#### Lambda

Package and upload code to Lambda, creating functions, which run in response to triggers that are either waited on or polled for

- http request
- s3 upload
- in-app activity

code is run on managed env

can choose language, memory and cpu your function is allowed

all lambdas run in an isolated environment

best for functions shorter than 15 mins

*can you import npm packages on lambda?*

you can also run containers on lambda

### How To Choose?

Consider the use case and the level of control necessary