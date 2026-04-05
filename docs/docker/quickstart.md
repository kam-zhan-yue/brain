1. Make a Dockerfile
2. `docker build -t app-name .`
3. `docker run -p 127.0.0.1:8000:8000 app-name`

## Commands
```shell
# lists containers
docker ps

# builds containers
docker build -t app .

# runs built containers
docker run -p 8000:8000 app

# stops containers
docker stop <container_id>

# executes commands in running containers
docker exec -it <container_id> <command>
```