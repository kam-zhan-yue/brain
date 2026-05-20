### Amazon Machine Images (AMI)
An AMI is an image that provides the software that is required to set up and boot an Amazon EC2 instance. Each AMI also contains a block device mapping that specifies the block device to attach to the instances that you launch
- You must specify an AMI when you launch an instance
- The AMI must be compatible with the instance type that you chose for your instance
- You can use an AMI provided by AWS, a public AMI, or an AMI that was shared or purchased from the marketplace
- You can launch multiple instances from a single AMI when you require multiple instances with the same configuration. You can use different AMIs to launch instances when you require instances with different configurations