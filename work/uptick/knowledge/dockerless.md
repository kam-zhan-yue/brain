[See documentation here](https://uptickhq.atlassian.net/wiki/spaces/ENGINEER/pages/2425389067/Dockerless+local+development)

Run migrations (whenever you see a migration checked-in)

```
python manage.py migrate
```

Running the backend

```
python manage.py runserver
```

Run background workers

```
python manage.py rundramatiq
```

Open a shell_plus session

```
python manage.py shell_plus
```

You can you run frontend sync commands

```
python manage.py sync_frontend
```

Running shell plus
```
python manage.py shell_plus
```

To load data from a dump file

```
`psql --username=$USER workforce < database_dump_file_path`
```

## Issues

### Is the server running on that host and accepting TCP/IP?

Running the server, but I encounter
```
psycopg2.OperationalError: connection to server at "localhost" (::1), port 5432 failed: Connection refused
        Is the server running on that host and accepting TCP/IP connections?
```

[Solutions are discussed here](https://stackoverflow.com/questions/37307346/is-the-server-running-on-host-localhost-1-and-accepting-tcp-ip-connections)

Try to 