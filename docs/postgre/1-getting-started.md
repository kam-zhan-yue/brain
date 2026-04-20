## 1.3 Creating a Database
You can create a new database using
```
createdb mydb
```

PostgreSQL allows you to create any number of databases at a given site. Database names must have an alphabetic first character and are limited to 63 bytes in length. If you don't want to use your database, you can remove it using

```
dropdb mydb
```

## 1.4 Accessing a Database
Once you have a created a database, you can access it by:
- Running the PostgreSQL interactive terminal program, which allows you to interactively enter, edit, and execute SQL commands
- Using a existing graphical frontend tool like pgAdmin or an office suite to create and manipulate a database
- Writing a custom application

Start up `psql` with
```
psql mydb
```

If you see the following, then you are a database superuser.
```
mydb=#
mydb=# \h // toggle help
mydb=# \q // quit
```


