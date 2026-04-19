## Creating and Dropping Tables
You can input this into an sql file like `create.sql` and pipe it into `psql`

```sql
CREATE TABLE weather (
  city    varchar(80),
  temp_lo int,
  temp_hi int,
  prcp    real,
  date    date
);
```

```bash
psql mydb < create.sql
```

You can drop tables using

```sql
DROP TABLE tablename;
```

## Populating Tables with Rows
The `INSERT` statement is used to populate a table with rows.
```sql
INSERT INTO weather VALUES ('San Francisco', 46, 50, 0.25, '1994-11-27');
```

You can also specify the column names

```sql
INSERT INTO weather (city, temp_lo, temp_hi, prcp, date)
  VALUES ('San Francisco', 46, 50, 0.25, '1994-11-27');
INSERT INTO cities (name, location)
  VALUES ('San Francisco', '(-194.0, 53.0)');
```

You can also use `COPY` if you have a data dump.

## Querying a Table
To retrieve data from a table, the table is queried. An SQL `SELECT` is used to do this.

```sql
SELECT * FROM weather;
SELECT city, (temp_hi+temp_lo)/2 AS temp_avg, date FROM weather;
SELECT DISTINCT city
    FROM weather
    ORDER BY city;
```

## Joins Between Tables
Queries can access multiple tables at once, or access the same table in such a way that multiple rows of the table are processed at the same time. Queries that access multiple tables at one time are called _join_ queries. They combine rows from one table with rows from a second table, with an expression specifying which rows are to be paired.

For example, to return all the weather records together with the location of the associated city, the database needs to compare the `city` column of each row of the `weather` table with the `name` column of all rows in the cities table, and select the pairs of rows where these values match. 

```sql
SELECT * FROM weather JOIN cities ON city = name;
```
- If there is a Hayward city in cities, but not in weather, then it will not be included in this query. This is because there is no matching entry in the `cities` table for Hayward, so the join ignores the unmatched rows in the `weather` table.
- There are two columns containing the city name. This is correct because the lists of columns from `weather` and `cities` are concatenated. You can list the output columns explicitly instead of using `*`
```sql
SELECT city, temp_lo, temp_hi, prcp, date, location
	FROM weather JOIN cities ON city = name;
```

It is widely considered good style to qualify all column names in a join query, so that the query won't fail if a duplicate column name is later added to one of the tables.

Join queries of this kind can also be written like below. This syntax predates JOIN/ON syntax. The explicit syntax of JOIN/ON makes its meaning eaiser to understand.
```sql
SELECT *
	FROM weather, cities
	WHERE city = name;
```

### LEFT OUTER JOIN
In order to also get rows in `weather` that are not present in `cities`, we want to scan the `weather` table and for each row to find the matching `cities` row(s). If no matching row is found, we want some "empty values" to be substituted for the `cities` table's columns.

This kind of query is called a left outer join. This is called a left outer join because the table mentioned on the left of the join operator will have each of its rows in the output at least once, whereas the table on the right will only have those rows output that match some row of the left table.

```sql
SELECT * FROM weather 
	LEFT OUTER JOIN cities ON weather.city = cities.name;
```

When outputting a left-table row for which there is no right-table match, empty values are substituted for the right-table columns.

> There are also right outer joins and full outer joins
> - Right outer joins will output all the rows in the right table
> - Full outer joins will output all the rows in both tables. Any non-matching rows will have blank columns
## SELF JOIN
We can also join a table against itself. Suppose we wish to find all the weather records that are in the temperature range of other weather records. So we need to compare all the `temp_lo` and `temp_hi` columns of each `weather` row to the `temp_lo` and `temp_hi` columns of other `weather` rows.
```sql
SELECT w1.city, w1.temp_lo AS low, w1.temp_hi AS high,
       w2.city, w2.temp_lo AS low, w2.temp_hi AS high
    FROM weather w1 JOIN weather w2
        ON w1.temp_lo < w2.temp_lo AND w1.temp_hi > w2.temp_hi;
```

```
     city      | low | high |     city      | low | high
---------------+-----+------+---------------+-----+------
 San Francisco |  43 |   57 | San Francisco |  46 |   50
 Hayward       |  37 |   54 | San Francisco |  46 |   50
(2 rows)
```

## Aggregate Functions
PostgreSQL supports *aggregate functions*. An aggregate function computes a single result from multiple input rows. For example, there are aggregates to compute the `count`, `sum`, `avg`, `max`, `min`, over a set of rows.

```sql
SELECT max(temp_lo) FROM weather;
```

```
 max
-----
  46
(1 row)
```

However, we cannot use aggregates in `WHERE` clauses. This is because the `WHERE` clause determines which rows will be included in the aggregate calculation; so obviously it has to be evaluated before aggregate functions are computed. We can achieve this instead by using a subquery.

```sql
SELECT city FROM weather
  WHERE temp_lo = (SELECT max(temp_lo) FROM weather);
```

This is OK because the subquery is an independent computation that computes its own aggregate separately from what is happening in the outer query.

Aggregates are also very helpful in combination with `GROUP BY` clauses. For example, we can get the number of readings and the maximum low temperature observed in each city with:

```sql
SELECT city, count(*), max(temp_lo)
  FROM weather
  GROUP BY city;
```

Which gives us one output per city. Each aggregate result is computed over the table rows matching that city. We can filter these grouped rows using `HAVING`.

```sql
SELECT city, count(*), max(temp_lo)
  FROM weather
  GROUP BY city
  HAVING max(temp_lo) < 40;
```

It is important to understand the interaction between aggregates and SQL's `WHERE` and `HAVING` clauses. The fundamental difference between `WHERE` and `HAVING` is this:
- `WHERE` selects input rows before groups and aggregates are computed. Thus, it controls which rows go into the aggregate computation
- `HAVING` selects group rows after groups and aggregates are computed. Thus, there `WHERE` clause must not contain aggregate functions; it makes no sense to try to use an aggregate to determine which rows will be inputs to the aggregates. The `HAVING` clause always contains aggregate functions. 

Another way to select rows that go into an aggregate computation is to use `FILTER`, which is a per-aggregate option.


```sql
SELECT city, count(*) FILTER (WHERE temp_lo < 45), max(temp_lo)
  FROM weather
  GROUP BY city;
```

`FILTER` is much like `WHERE`, except that it removes rows only from the input of the particular aggregate function that it is attached to. Here, the `count` aggregate only contains rows with `temp_lo` below 45; but the `max` aggregate is still applied to all rows (when grouped by city).

## Updates
You can update existing rows using the `UPDATE` command. Suppose you discover the temperature readings are all off by 2 degrees after November 28. You can correct the data as follows:

## Deletions
Rows can be removed from a table using the `DELETE` command. 

```sql
DELETE FROM weather WHERE city = 'Hayward';
```

Be wary of statements of the form. Without any qualification, `DELETE` will remove all rows from the given table. The system will not request confirmation before doing this!

```sql
DELETE from tablename;
```
