# Account Security
Amazon Identity and Access Management (IAM) lets you define individual users with permissions across AWS resources. IAM can be used to grant employees and applications federated access to the AWS Management Console and AWS service APIs, making use of existing identity systems.
- Normally it is not recommended to access the console as the root user as they can access all AWS services
- Programs that access IAM need a Key ID and a Secret Access Key
- Setting IAM Policies allow you to set permissions for various services
	- e.g. AmazonS3FullAccess, AmazonS3ReadOnlyAccess
- You can also set user group permissions
- An IAM policy is the json/yaml code that grants or denies access to resources. An IAM role will have an IAM policy attached to it which defines the permissions to that role. Then, a user or service can be allowed to assume that role and be granted the permissions defined by the role's IAM policy.

- Identity-based policies are attached to an IAM user, group, or role. These policies let you specify what that identity can do (permissions).
	- E.g. you can attach that policy to an IAM user named John, stating that he is allowed to perform the Amazon EC2 RunInstances action.
	- The policy could further state that John is allowed to get items from a DynamoDB table called MyCompany.
- Resource-based policies are attached to a resource.
	- E.g. you can attache resource-based policies to Amazon S3 buckets, SQS queues, VPC endpoints, AWS Key Management Service encryption keys, and DynamoDB tables
	- You can specify who has access to the resource and what actions they can perform on it 
- A mix of identity-based policies and resource-based policies allow you to have the most flexibility
- An explicit Deny override an Allow
- [Policy evaluation logic can be found here](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html)