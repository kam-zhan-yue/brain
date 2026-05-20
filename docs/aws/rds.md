Amazon Relational Database Service (Amazon RDS) is a web service that makes it easier to set up, operate, and scale a relational database in the cloud.
- You can use database engines like PostgreSQL, MySQL, etc
- RDS manages backups software patching, automatic failure detection, etc
- RDS can automate backups, or you can manually create your own backups
- You can get high availability with a primary DB instance and a synchronous secondary DB instance that you can fail over to when problems occur
- You can control access using AWS Identity and Access Management (IAM)
- You can also help protect your databases by putting them on a VPC

This is an example of a dynamic website that uses Amazon RDS DB instances for database storage:

![[Pasted image 20260519113648.png]]
- Elastic Load Balancing: AWS routes user traffic through Elastic Load Balancing. A load balancer distributes workloads across multiple compute resources, such as virtual services. Here, the ELB forwards client requests to application serverrs
- Application Servers: Interact with the RDS DB instances. An application server in AWS is typically hosted on EC2 instances, which provide scalable computing capacity. The application servers reside in public subnets with different Availability Zones (AZs) within the same VPC
- RDS DB Instances: The EC2 application servers interact with RDS DB instances. The DB instances reside in private subnets within different Availability Zones within the same VPC. Because the subnets are private, no requests from the Internet are permitted. The primary DB instance replicates to another DB instance, called a read replica.