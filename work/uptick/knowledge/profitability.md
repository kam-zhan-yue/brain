## Quick History
- `costing.py` : ad-hoc and task-centric (1 row / task)
- Ledger Transactions: raw SQL, entity-centric (1 row / instance), bad performance
- Aggregate Profitability: raw SQL, task-centric. No taskless profitability (no service quotes, etc). Also no transparency to the profitability calculations
- Transactional Profitability: Precalculated, Django ORM, event centric (2 rows / event)

## Transaction Overview

Transactions are atomic events
- `PurchaseOrder` submitted
- `ServiceTask` cost price updated
- `Task` deleted

Transactions have key fields:
- What happened
- Who did it

Each entry has a mirrored pair (double entry). All entries in a transaction are balanced (net 0 sum).
- `estimated_cost` = -250
- `committed_cost` = 250

## Data Capture

### TransactionBuilderService

Encapsulates all possible profitability events `builder.{{entity}}.{{event}}`

Chain together to build entries, then send to the background for processing. 

```python
# invoke iimmediately
TransactionBuilderService(desription="Purchase Order Bill Line Item created").purchaseorderbilllineitem.created(instace).process_in_bg()

# chain together transactions
txn_builder = TransactionBuilderService("Purchase Order created")
txn_builder.purchaseorderlineitem.created(poli)
txn_builder.servicetask.updated(st).process_in_bg()
```

### Commandments

- Must be called immediately after a 'DB Action' (save, etc) for an entity that affects profitability
- Must be called indiscriminately - the service decides whether there's anything to capture

## Rollout

### Phase 1: Capture, Store, Validate
- Compare with ad-hoc calculations, address disrepancies
- Running in shadow mode

### Phase 2: Cutover
- Staged rollout via FF once confiden