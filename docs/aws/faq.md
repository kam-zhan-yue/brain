## Where do I get API keys?
Inside of the AWS Access Portal, normally https://company.awsapps.com/start, you can find the Access Tokens for each account name. These will have:
- SSO Start URL
- SSO Region
```
aws configure sso
> Input your SSO Start URL
> Input your SSO region

aws sso login
```

## AWS and Terraform Setup

Firstly we need to `aws sso login` and call `aws sts get-caller-iden`
```

```

### Normal Structure
When the user goes to a public URL,
1. Internet > Internet Gateway: The request enters AWS and hits the VPC's internet gateway, which allows traffic between the Internet and the VPC.
2. Internet Gateway > Route Table: The route table for the public subnet would have rules 0.0.0.0/0 > igw, which routes all inbound traffic to resources in the subnet
3. Route Table > Security Group: The security group acts as a firewall. It checks inbound rules (such as allowing port 80 from 0.0.0.0/0).
4. Security Group > EC2 Instance: The request reaches the EC2 instance on port 80
