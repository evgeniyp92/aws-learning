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