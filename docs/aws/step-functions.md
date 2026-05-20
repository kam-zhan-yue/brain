Step functions can create state machines to build distributed applications, orchestrate microservices, etc.
- Based on _state machines_ and _tasks_
- Step machines are called workflows, which are a series of event-driven steps. Each step in a workflow is a _state_
- The work in your state machine tasks can also be done using Activities which are workers that exist outside of Step Functions.

In the Step Functions' console, you  can visualise, edit, and debug your application's workflow. You can examine each step in your workflow to make sure that your application runs in order and as expected.

There are two workflow types:
- Standard workflows are ideal for long-running, auditable workflows as they have execution history and visual debugging. Standard workflows have an exactly-once workflow execution and can run up to one year. This means that every step will execute exactly once.