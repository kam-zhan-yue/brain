## `psql`
Start the `psql` CLI interface with 
```
psql [database] [username]

psql workforce kamzhanyue
```

To list tables, do
```
\dt
```

## Database URL
To discover your local connection details, you can do:
```
psql mydb
\conninfo
> You are connected to database "mydb" as user "kamzhanyue" via socket in "/tmp" at port "5432".
```

If there is no password (happens when the username is the same as the root administrator), then the database URL would be
```
postgres://kamzhanyue@localhost:5432/mydb
```

