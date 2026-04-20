## Views
Suppose the combined listing of weather records and city location is of particular interest to your application, but you don't want to type the query each time you need it. You can create a _view_ over the query, which gives a name to the query you can refer to like an ordinary table:

```sql
CREATE VIEW myview AS
  SELECT name, temp_lo, temp_hi, prcp, date, location
    FROM weather, cities
    WHERE city = name;
```

Making liberal use of views is a key aspect of good SQL database design. Views allow you to encapsulate details of the structure of your tables, which might change as your application evolves, behind consistent interfaces.

Views can be used in almost any place a real table can be used. Building views upon other views is not uncommon.

## Foreign Keys
Recall the `weather` and `cities` tables. If you want to make sure that no one can insert rows in the `weather` table that do not have a matching entry in the `cities` table, it is known as maintaining the _referential integrity_ of your data.

The new declaration of the tables would look like:

```sql
CREATE TABLE  cities (
  name      varchar(80) primary key,
  location  point
);

CREATE TABLE weather (
  city      varchar(80) references cities(name),
  temp_lo   int,
  temp_hi   int,
  prcp      real,
  date      date
);
```

## Transactions
_Transacitons_ are a fundamental concept of all database systems. The essential point of a transaction is that it bundles multiple steps into a single, all-or-nothing operation. The intermediate states between the steps are not visible to other concurrent transactions, and if some failure occurs that prevents the transaction from completing, then none of the steps affect the database at all.

A transaction is said to be _atomic_, it either happens complete or not at all.

We also want to guarantee that once a transaction is completed and acknowledged by the database system, it has indeed been permanently recorded and won't be lost even if a crash ensues shortly thereafter. For example, if we are recording a cash withdrawal, we don't want any change that the debit to their account disappears. A transactional database guarantees that all the updates made by a transaction are logged in permanent storage before the transaction is reported complete.

Another important property of transactional databases is closely related to the notion of atomic updates: when multiple transactions are running concurrently, each one should not be able to see the incomplete changes made by others. The updates made so far by an open transaction are invisible to other transactions until the transaction completes, whereupon all the updates become visible simultaneously.

In PostgreSQL, a transaction is set up by surrounding the SQL commands of the transaction with `BEGIN` and `COMMIT` commands.

```sql
BEGIN;
UPDATE // sql updates and stuff;
COMMIT;
```

If partway through the transaction, we decide we do not want to commit, we can issue the command `ROLLBACK` instead of `COMMIT`, and all our updates so far will be cancelled.

PostgreSQL actually treats every SQL statement as being executed within a transaction. If you do not issue a `BEGIN` command, then each individual command has an implicit `BEGIN` and `COMMIT` command wrapped around it. A group of transactions surrounded by `BEGIN` and `COMMIT` is sometimes called a _transaction block_.

It's possible to control the statements in a transaction in a more granular fashion through the use of _savepoints_. Savepoints allow you to selectively discard parts of your transaction, while committing the rest. After defining a savepoint with `SAVEPOINT`, you can, if needed, roll back to the savepoint with `ROLLBACK TO`. All the transaction's database changes between defining the savepoint and rolling back to it are discarded, but changes earlier are kept.

After rolling back to a savepoint, it continues to be defined, so you can roll back to it multiple times. Conversely, if you are sure you won't need to rollback to a particular savepoint again, it can be released, so that the system can free some resources. Either releasing or rolling back to a savepoint will automatically release all savepoints that were defined after it.

All this is happening within the transaction block, so none of it is visible to other database sessions. When and if you commit the transaction block, the committed actions become visible as a unit to other sessions, while the rolled-back actions never become visible at all.

Consider a back session where we want to debit $100.00 from Alice's account, and credit Bob's account, only to find later that we should have credited Wally's account. We could do it by using:

```sql
DROP TABLE accounts;

CREATE TABLE accounts (
  name      varchar(80) primary key,
  balance   real
);

INSERT INTO accounts (name, balance)
  VALUES
  ('Alice', 500.0),
  ('Bob', 400.0),
  ('Wally', 300.0);

BEGIN;
UPDATE accounts SET balance = balance - 100.00
	WHERE name = 'Alice';
SAVEPOINT my_savepoint;
UPDATE accounts SET balance = balance + 100.00
	WHERE name = 'Bob';
-- oops, meant to use Wally's account
ROLLBACK TO my_savepoint;
UPDATE accounts SET balance = balance + 100.00
	WHERE name = 'Wally';
COMMIT;

SELECT * FROM accounts;
```

`ROLLBACK TO` is the only way to regain control of a transaction block that was put in an aborted state by the system due to an error, short of rolling it back completely and starting again.

## Window Functions
A _window function_ performs a calculation across a set of table rows that are somehow related to the current row. This is comparable to the type of calculation that can be done with an aggregate function. However, window functions do not cause rows to be grouped into a single output row like non-window aggregate calls would. Instead, the rows retain their separate identities. Behind the scenes, the window function is able to access more than just the current row of the query result.
