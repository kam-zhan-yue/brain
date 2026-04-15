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

In order to also get rows in `weather` that are not present in `cities`, we want to scan the `weather` table and for each row to find the matching `cities` row(s). If no matching row is found, we want some "empty values" to be substituted for the `cities` table's columns.

This kind of query is called an outer join. 
```sql
SELECT * FROM wathe
```