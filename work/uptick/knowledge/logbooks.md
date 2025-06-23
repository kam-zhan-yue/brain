The link between a task and a digital logbook is the ref that defines:
- The tenant name
- The task id

```python
ref = parse.quote(
	f"com.uptickhq.workforce?org={current_tenant.name}&task={task.id}"
)
```

To link something, we will have to go into the admin panel of logbooks-staging.onuptick.com/admin/forms/response and change the ref field on a response there.