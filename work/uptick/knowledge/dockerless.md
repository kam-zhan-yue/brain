[See documentation here](https://uptickhq.atlassian.net/wiki/spaces/ENGINEER/pages/2425389067/Dockerless+local+development)

Running tests
```shell
DJANGO_SETTINGS_MODULE=abas.settings.runtests python manage.py test --keepdb
```

Backend format and checking
```bash
ruff format
ruff check
mypy abas
```

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
First you need to either
```
dropdb workforce
```

or create a new database
```
createdb aesg
```

```
`psql --username=$USER workforce < database_dump_file_path`
```


## Issues
## Errno 48: Address already in use
We first need to find the rogue process, then kill it.
```
ps -a
```

Locate anything that seems related then do
```
kill -9 PID
```

### Is the server running on that host and accepting TCP/IP?

Running the server, but I encounter
```
psycopg2.OperationalError: connection to server at "localhost" (::1), port 5432 failed: Connection refused
        Is the server running on that host and accepting TCP/IP connections?
```

[Solutions are discussed here](https://stackoverflow.com/questions/37307346/is-the-server-running-on-host-localhost-1-and-accepting-tcp-ip-connections)

**Solution**: Most likely `postgresql` is errored or hasn't started.


Do
```
brew services
```

You might see
```
Name          Status   User       File
postgresql@14 error  1 kamzhanyue ~/Library/LaunchAgents/homebrew.mxcl.postgresql@14.plist
redis         started  kamzhanyue ~/Library/LaunchAgents/homebrew.mxcl.redis.plist
unbound       none
```

Need todo 


Run postgrew manually
```
➜ /opt/homebrew/opt/postgresql@14/bin/postgres -D /opt/homebrew/var/postgresql@14

2025-09-12 16:07:16.037 AEST [14617] FATAL:  lock file "postmaster.pid" already exists
2025-09-12 16:07:16.037 AEST [14617] HINT:  Is another postmaster (PID 742) running in data directory "/opt/homebrew/var/postgresql@14"?
```

Then run 
```
kill -9 PID
```


### Issues
```
django.db.utils.OperationalError: could not open extension control file "/opt/homebrew/share/postgresql@14/extension/postgis.control": No such file or directory
```

This is because `brew install postgis` ended up installing for 17 and 18
```shell
> brew list | grep postgresql 
postgresql@14 

> find /opt/homebrew/share -name postgis.control /opt/homebrew/share/postgresql@17/extension/postgis.control /opt/homebrew/share/postgresql@18/extension/postgis.control

```

- [ ] It seems like symlinking it doesn't work. So we will need to 

## New Database
```shell
createdb develop
psql --username=$USER develop < var/develop/dump
```

Then change the value in .env

> NOTE: I still can't figure out how to make it not use 'workforce'