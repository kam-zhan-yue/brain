Simple Queue Service (SQS) is a hosted queue that can be integrated with other AWS services.

There are three main parts of the queue system: the components of the distributed system, the queue, and the messages in the queue.
- Components: There are producers, components that send messages to the queue, and consumers, components that receive messages from the queue. 
- Queue: The queue stores messages across Amazon SQS services.
- Messages: Created by components and stored in queues

### Message Lifecycle
- A producer sends a message to the queue, and the messages is distributed across the Amazon SQS servers redundantly
- When a consumer is ready to process messages, it consumes messages from the queue, and the message is returned. While the message is being processed, it remains in the queue and isn't returned to subsequent requests for the duration of the visibility timeout
- The consumer deletes the message from the queue to prevent the message from being received and processed again when the visibility timeout expires.