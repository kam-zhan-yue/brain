Marc is currently uplifting our profitability calculations. We have a current N+1 problem in `get_unbilled_amount` that is using custom logic to calculate profitability things. Since this will be included in the cemented profitability stuff, we need to make sure that they are equivalent. We can use the profitability glossary to analyse this.

In the profitability table, the unbilled amount is referred to as the `Billable` amount. This is what is currently billable but yet to be sent to the client. Billable will reduce as the items on the task become Invoiced. To calculate, this is the 

```
Historical Billable - Invoiced
```

Historical Billable is calculated for the following
- ServiceTasks where `performed_date is not None and is_billable is True`
- TaskSessions where `is_billable is True`
- SubTask where `deleted=None and billingocntract=None`
- SubTaskLineItem where `subtask.deleted=None and subtask.billingcontract != None`

First, we need to analyse what the current code is doing.

In the dispatch modals, we are getting `task.get_unbilled_amount`. This calls
```python
LineItem.prepare_for_task(self, performed_only=True, uninvoiced_only=True)
```

This does a few things
- Gets all the child tasks and groups them if it is the parent task
- Calculates the line items for `task.subtasks`
	- Gets all the subtasks with a billing contract (conflict)
	- Not sure how quantity is calculated here, but it doesn't say `site_price`
- Calculate line items for `task.servicetasks` where they are performed and uninvoiced
	- Gets all servicetasks where `is_billable=True`
	- Excludes if `product__isnull=True` (conflict)
	- Groups by product
	- Simply calculates the quantity and unit_price, grouped by product on each line item
- Calculates line items for `task.tasksessions` where the charge time is `TIMEANDMATERIALS`
	- Gets all tasksessions where `is_billable=True`
	- Quantity is `sell_hours`
	- Price is `sell_rate`
- Calculates the line items for the child tasks recursively

So, there are obviously differences here I think. Probably need to follow up with Marc