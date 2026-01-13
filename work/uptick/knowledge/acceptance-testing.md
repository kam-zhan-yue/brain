I encountered this error a lot:

```python
Error 111 connecting to 0.0.0.0:6379. Connection refused.
```

Something with docker was not working.

Upon analysing what was really going on, I found that:
```
mise test:acceptance
```

Calls `mise start:acceptance -b` to build the docker container.

So I decided to do it myself

```shell
export COMPOSE_FILE="$PATH/builds/e2e/docker-compose.yml"
docker compose build
docker compose up -d
```

Then, running the acceptance tests worked...