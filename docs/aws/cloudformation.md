CloudFormation is a service that helps you model and set up your AWS resources so that you can spend less time managing those resources and more time focusing on your applications that run in AWS.
- Simplifies infrastructure management as it handles an Auto Scaling group, an Elastic Load Balancing load balancer, etc
- Allows you to replicate your infrastructure as you can reuse your CloudFormation template to create resources in a consistent an dreplicable manner

## Templates
A CloudFormation template is a YAML or JSON formatted text file. These are ud a blueprints for building your AWS resources. E.g. in a template, you can describe an Amazon EC2 instance, such as the instance type, the AMI ID, the block device mappings, and its Amazon EC2 key pair name.
```yaml
AWSTemplateFormatVersion: 2010-09-09
Description: A sample template
Resources:
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-0ff8a91507f77f867
      InstanceType: t2.micro
      KeyName: testkey
      BlockDeviceMappings:
        - DeviceName: /dev/sdm
          Ebs:
            VolumeType: io1
            Iops: 200
            DeleteOnTermination: false
            VolumeSize: 20
```

- You can also specify multiple resources in a single template and configure these resources to work together
- E.g. you can modify the previous template to include an Elastic IP address and associate it with the Amazon EC2 instance.

## Stacks
When you use CloudFormation, you manage related resources as a single unit called a stack. You create, update, and delete a collection of resources by creating, updating, and deleting stacks.
- All the resources in a stack are defined by the stack's template
- E.g. if you create a template that includes an Auto Scaling group. a load balancer, and a RDS database instance, you create the stack by submitting the template and CloudFormation provisions the resources for you

## How it works
When you use CloudFormation to create your stack, it makes underlying service calls to AWS to provision and configure the resources described in your template. 
- The calls that CloudFormation make are declared by your template
- E.g. if you have a template that describes an EC2 instance with a `t2.micro` instance type, CloudFormation calls the EC2 create instance API and specifies the instance type to `t2.micro`.

## CDK
The AWS Cloud Development Kit (AWS CDK) is an open-source software development framework for defining cloud infrastructure in the code and provisioning it through CloudFormation. The AWS CDK consist of two primary parts:
- AWS CDK Construct Library - A collection of pre-written modular and reusable pieces of code, called constructs, that can be used to develop infrastructure modulaly
- AWS CDK Toolkit - Tools that you can use to manage and interact with your CDK apps, such as performing synthesis or deployment

### Difference to Terraform
Both CloudFormation and Terraform are used to spin up cloud instances with a pre-configured template. However, Terraform is configured for multi-cloud platforms.