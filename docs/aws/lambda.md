AWS is a compute service that runs code without the need to manage servers. Code runs, scaling up and down automatically with pay-per-use pricing

When using Lambda, you are only responsible for your own code. Lambda runs your code on high-availability compute infrastructure and manages all the computing resources, including server and system maintenance, capacity provisioning, automatic scaling, and logging.

Lambda is a serverless, event-driven compute service.
- You write and organise your code in Lambda functions
- You control security and access through Lambda permissions
- Event sources and AWS services trigger your Lambda functions, padding event data in JSON format
- Lambda runs your code with language-specific runtimes

## Use Cases
- File Processing: Process files automatically when uploaded to S3
- Long-running workflows: Durable lambda functions can build stateful, multi-step workflows that can run for up to one year. It is good for order processing, approval workflows, and complex data pipelines
- Database operations and integration examples: Respond to database changes and automate data workflows
- Scheduled and periodic tasks: Run automated operations on regular schedules
- Stream processing: Process real-time data streams for analytics and monitoring