DynamoDB is a serverless, fully managed, distributed NoSQL database with single-digit millisecond performance at any scale.
- Serverless: Provides zero downtime maintenance and has an on-demand capacity mode that offers pay-as-you-go pricing. DynamoDB instantly scales your tables up or down to adjust for capacity and maintains performance with zero administration.
- NoSQL: Supports both key-value and document data models. It does not support a JOIN operator
- Fully Managed: Handles the heavy lifting of managing a database by handling setup, configurations, maintenance, high availability, hardware provisioning, security, backups, etc. When you create a DynamoDB table, it is instantly ready for production workloads
- Single-digit millisecond performance: Optimised for high performance by omitting performant-heavy features like JOIN operators.

### Use Cases
- Financial Service Applications: If you have live trading, loan management, loan generation, etc, DynamoDB global tables can respond to events and serve traffic.
- Gaming Applications: Can be used to store game state, player data, session history, and leaderboards. DynamoDB is well suited for scale-out architectures and quickly scales throughput from zero with no cold start.
- Streaming Applications: DynamoDB is often used as a metadata index for content, content management service, or to serve near real-time sports statistics. They also use DynamoDB to run user watchlist and bookmarking services and process billions of daily customer events for generating recommendations.