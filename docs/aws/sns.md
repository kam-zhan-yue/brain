Amazon Simple Notification Service (SNS) is a fully managed service that provides message delivery from publishers (producers) to subscribers (consumers). Publishers communicate asynchronously with subscribers by sending messages to a _topic_, which is a logical access point and communication channel.

### How it works
- Publishers send messages to a topic, which acts as a communication channel.
- Subscribers to an SNS topic can receive messages through different endpoints, depending on their use case, such as:
	- Amazon SQS
	- Amazon Lambda,
	- HTTP(S) endpoints
	- Email
- SNS supports both Application-to-Application (A2A) and Application-to-Person (A2P) messaging, giving flexibility to send messages between different applications or directly to mobile phones, etc.

### Use Cases
- Application integration: You can develop an application that publishes a message to an SNS topic whenever an order is placed for a product. The SQS queues that are subscribed to the SNS topic receive identical notifications for the new order. An EC2 server instance attached to the SQS queues can handle the processing or fulfilment of the order. Another EC2 instance to a data warehouse and also subscribe to analyse the order.
- Application Alerts: Application and system alerts are notifications that are triggered by predefined thresholds. SNS can send these notifications to specified users via SMS and email. E.g. when your EC2 Auto Scaling group changes, or if a new file is uploaded to an S3 bucket.
- User Notifications: SNS can send push email messages and text messages to individuals or groups.
- Mobile Push Notifications: SNS enable you to send messages directly to mobile apps.