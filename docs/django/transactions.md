[See documentation here](https://docs.djangoproject.com/en/5.2/topics/db/transactions/)
Django's default behaviour is to run in autocommit mode. Each query is immediately commited to the database, unless a transaction is active. Django uses transactions or savepoints automatically to guarantee the integrity of ORM operations that require multiple queries, especially `delete()` and `update()` queries.

### Tying transactions to HTTP requests
A common way to handle transactions on the web is to wrap each request in a transaction.
- Before calling a view function, Django starts a transaction
- If the response is produced without problems, Django commits the transaction
- If the view produces an exception, Django rolls back the transaction

You may perform sub-transactions using savepoints in your view code, typically with the `atomic()` context manager. However, at the end of the view, either al or none of the changes will be committed.


## Controlling transactions explicitly
Django provides a single API to control database transactions
## atomic
Atomicity is the defining property of database transactions. `atomic` allows us to create a block of code within which the atomicity on the database is guaranteed. If the block of code is successfully completed, the changes are committed to the database. If there is an exception, the changes are rolled back.

`atomic` blocks can be nested. In this case, when an inner block completes successfully, its effects can still be rolled back if an exception is raised in the outer block at a later point.

It is sometimes useful to ensure an `atomic` block is always the outermost `atomic` block, ensuring that any database changes are committed when the block is exited without errors. This is known as durability and can be achieved by setting `durable=True`. If the `atomic` block is nested within another, it raises a `RuntimeError`

`atomic` as a decorator
```python
from django.db import transaction

@transaction.atomic
def viewfunc(request):
    # This code executes inside a transaction.
    do_stuff()
```

`atomic` as a context manager
```python
from django.db import transaction

def viewfunc(request):
    # This code executes in autocommit mode (Django's default).
    do_stuff()

    with transaction.atomic():
        # This code executes inside a transaction.
        do_more_stuff()
```

`atomic` with try/catch
```python
from django.db import IntegrityError, transaction

@transaction.atomic
def viewfunc(request):
    create_parent()

    try:
        with transaction.atomic():
            generate_relationships()
    except IntegrityError:
        handle_exception()

    add_children()
```

In the above, even if `generate_relationships()` causes a database error by breaking an integrity constraint, you can execute queries in `add_children()`, and the changes from `create_parent()` are still there and bound to the same transaction. Note that any operations attempted by `generate_relationships()` will already have been rolled back safely when `handle_exception()` is called, so the exception handler can also operate on the database if necessary.

> Avoid catching exceptions inside `atomic`! If you catch and handle exceptions inside an `atomic` block, you may hide from Django the fact that a problem has happened. This can result in unexpected behaviour.
> This is mostly a concern for `DatabaseError` and its subclasses such as `IntegrityError`

In order to guarantee atomicity, `atomic` disables some APIs. Attempting to commit, roll back, or change the autocommit state of the database connection within an `atomic` block wil raise an exception. 

Under the hood, Django's transaction management code:
- opens a transaction when entering the outermost `atomic` block
- creates a savepoint when entering an inner `atomic` block
- releases or rolls back to the savepoint when exiting an inner block
- commits or rolls back the transaction when exiting the outermost block

If an exception occurs, Django will perform the rollback when exiting the first parent block with a savepoint if there is one, and the outermost block otherwise.

## Transactions
A transaction is an atomic set of database queries. Even if your program crashes, the database guarantees that either all the changes will be applied, or none of them.

Once you're in a transaction, you can choose either to apply the changes you've performed until this point with `commit()` or to cancel them with `rollback()`
- These functions take a `using` argument which should be the name of a database.
- If it isn't provided Django uses the "default" database

Django will refuse to commit or to rollback when an `atomic()` block is active, because that would break atomicity.

## Savepoints
A savepoint is a marker within a transaction that enables you to roll back part of a transaction, rather than the full transaction. When the `atomic()` decorator is nested, it creates a savepoint to allow partial commit or rollback. You are strongly encouraged to use `atomic()` rather than the savepoint functions.
- `savepoint` creates a new savepoint. This marks a point in the transaction that is known to be in a "good" state. Retuns the savepoint id (sid)
- `savepoint_commmit` releases savepoint `sid`. The changes performed since the savepoint was created become part of the transaction
- `savepoint_rollback` rolls back the transaction to savepoint `sid`

Below demonstrates the use of savepoints

```python
from django.db import transaction

# open a transaction
@transaction.atomic
def viewfunc(request):
    a.save()
    # transaction now contains a.save()

    sid = transaction.savepoint()

    b.save()
    # transaction now contains a.save() and b.save()

    if want_to_keep_b:
        transaction.savepoint_commit(sid)
        # open transaction still contains a.save() and b.save()
    else:
        transaction.savepoint_rollback(sid)
        # open transaction now contains only a.save()
```

Savepoints may be used to recover from a database error by performing a partial rollback. If you're doing this inside an `atomic()` block, the entire block will still be rolled back, bceause it doesn't know you've handled the situation at a lower level. To prevent this, you can control the rollback behaviour with the following functions
- `get_rollback`
- `set_rollback`

Setting the rollback flag to `True` forces a rollback when exiting the innermost atomic block. This may be useful to trigger a rollback without raising an exception.

## TransactionManagementError
If a `DatabaseError` occurs, the transaction is broken and Django will perform a rollback at the end of the `atomic` block. If you attempt to run database queries before the rollback happens, Django will raise a `TransactionManagementError`.