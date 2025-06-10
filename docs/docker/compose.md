[See Documentation](https://docs.docker.com/compose/)

Docker Compose defines and runs multi-container applications, simplifying entire application stacks and making it easy to manage services, networks, and volumes in a single YAML configuration file. Through a single command, you create and start all the services from the configuration file.

Compose works in all environments; production, staging, development, testing, as well as CI workflows.

## The Compose Application Model

- A **service** is an abstract concept implemented on platforms running the same container image, and configuration, one or more times
- Services communicate with each other through **networks**, platform capability abstractions that establish an IP route between containers within services connected together.
- Services store and share persistent data into **volumes**.
- A **secret** is a specific flavour of configuration data for sensitive data that should not be exposed without security considerations. Secrets are made available to services as files mounted onto their containers.
- A **project** is an individual deployment of an application specification on a platform. A project's name is used to group resources together and isolate them from other applications or other installation of the same Compose-specified application with distinct parameters.

## The Compose File

- The default path for a Compose is `compose.yaml` in the working directory.
- Multiple Compose files can be merged together to define the application model.

## CLI

- The Docker CLI provides a `docker compose` command
- `docker compose up` starts all the services defined in the `compose.yaml` file
- `docker compose down` stops and removes the running services
- `docker compose logs` monitors the output of your running containers
- `docker compose ps` lists all the services along with their current status

## Example

Consider an application split into a frontend web application and a backend service.
- The frontend is configured at runtime with a HTTP configuration file managed by infrastructure, providing an external domain name, and a HTTPS server certificate injected by the platform's secured secret store.
- The backend stores data in a persistent volume.
- Both services communicate with each other on an isolated back-tier network, while the frontend is also connected to a front-tier network and exposes port 443 for external usage.

![[docker-example.png]]****

The application has the following parts.
- 2 services, backed by Docker images: `webapp` and `database`
- 1 secret (HTTPS certificate), injected into the frontend
- 1 configuration (HTTP), injected into the frontend
- 1 persistent volume, attached to the backend
- 2 networks

The `docker compose up` command starts the `frontend` and `backend` services, creating the necessary networks and volumes, and injects the configuration and secret into the frontend service.

The `docker compose ps` provides a snapshot of the current state of the services, making it easy to see which containers are running, their status, and the ports they are using.


```yaml
services:
  frontend:
    image: example/webapp
    ports:
      - "443:8043"
    networks:
      - front-tier
      - back-tier
    configs:
      - httpd-config
    secrets:
      - server-certificate

  backend:
    image: example/database
    volumes:
      - db-data:/etc/data
    networks:
      - back-tier

volumes:
  db-data:
    driver: flocker
    driver_opts:
      size: "10GiB"

configs:
  httpd-config:
    external: true

secrets:
  server-certificate:
    external: true

networks:
  # The presence of these objects is sufficient to define them
  front-tier: {}
  back-tier: {}
```