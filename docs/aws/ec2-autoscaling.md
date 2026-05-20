EC2 Auto Scaling ensures that you have the correct number of Amazon EC2 instances available to handle the load for your application. 

- You create collections of EC2 instances, called Auto Scaling group
- You specify the minimum and maximum number of instances in each Auto Scaling group

 With Amazon EC2 Auto Scaling, your EC2 instances are organised into Auto Scaling groups so that they can be treated as a logical unit for the purposes of scaling and management. Auto Scaling groups use launch templates (or launch configurations) as configuration templates for their EC2 instances.

The main features of Auto Scaling are:
 - Monitoring the health of running instances: EC2 health checks can replace terminated or impaired instances to maintain your desired capacity
 - Custom health checks: You can define custom health checks that are specific to your application to verify that it's responding as expected
 - Balancing capacity across Availability Zones: You can specify multiple AZs for your Auto Scaling group, and the instances can be balanced evenly across the AZs as the group scales. This provides high availability and resiliency
 - Multiple instance types and purchase options: With a single Auto Scaling group, you can launch multiple instance types and purchase options, allowing you to optimise costs through Spot Instance usage.
 - Load Balancing: You can use ELB load balancing and health checks to ensure an even distribution of application traffic to your healthy instances.
 - Scalability: 