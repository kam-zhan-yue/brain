## Quickstart
You can get PostgreSQL running in Docker with:

```bash
docker pull postgres
docker run --name some-postgres -e POSTGRES_PASSWORD=pw postgres
```

However, when you stop the container, your data will disappear. This is because containers use an ephemeral filesystem. When a container is removed, everything inside it, including your database files, is deleted.

### Named Volumes
Named volumes are Docker-managed storage locations that persist independently of containers. Docker handles the filesystem location, permissions, and lifecycle.  

```bash
docker run --rm --name postgres-dev \
  -e POSTGRES_PASSWORD=mysecretpassword \
  -p 5432:5432 \
  -v postgres_data:/var/lib/postgresql \
  -d postgres:18
```

The `-v postgres_data:/var/lib/postgresql` flag mounts a named volume called `postgres_data` to PostgreSQL's data directory. If the volume doesn't exist, Docker creates it automatically.

You can see all your volumes with
```bash
> docker volume ls --filter name=postgres_data
DRIVER    VOLUME NAME
local     postgres_data
```

You can inspect a volume to see its details:
```bash
docker volume inspect postgres_data
[
    {
        "CreatedAt": "2025-01-05T10:30:00Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/postgres_data/_data",
        "Name": "postgres_data",
        "Options": null,
        "Scope": "local"
    }
]
```