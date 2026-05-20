A virtual private cloud (VPC) is a virtual network dedicated to your AWS account. It is logically isolated from other virtual networks in the AWS Cloud. You can launch AWS resources, such as EC2 instances, into your VPC.
## VPC Peering
A VPC peering connection is a networking connection between two VPCs that enables your to route traffic between them using IPv4 addresses or IPv6 addresses.
- Instances in either VPC can communicate with each other as if they are within the same network
- You can create a VPC peering connection between your own VPCs, or with a VPC in another AWS account.
- The VPCs can be in different Regions

AWS uses the existing infrastructure of a VPC to create a VPC peering connection; it is neither a gateway nor a VPN connection, and does not rely on a separate piece of physical hardware. There is no single point of failure for communication or a bandwidth bottleneck.

A VPC peering connection helps you to facilitate the transfer of data. E.g. if you have more than one AWS account, you can peer the VPCs across those accounts to create a file sharing network.