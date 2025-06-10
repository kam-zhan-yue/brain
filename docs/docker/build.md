Docker Build implements a client-server architecture, where:
- Client: Buildx is the client and the user interface for running and managing builds
- Server: BuildKit is the server, or builder, that handles the build execution.

When you invoke a build, the Buildx client sends a build request to the BuildKit backend. BuildKit resolves the build instructions and executes the build steps. The build output is either sent back to the client or uploaded to a registry such as Docker Hub.

When you invoke the `docker build` command, you're using Buildx to run a build using the default BuildKit bundled with Docker.

## Buildx

The `docker build` command is a wrapper around Buildx.

## BuildKit

BuildKit is the daemon process that executes the build workloads. Buildx interprets your build command and sends a build request to the BuildKit backend. The build request includes:
- The Dockerfile
- Build arguments
- Export options
- Caching options

## Dockerfile

Docker builds images by reading the instructions from a Dockerfile. A Dockerfile is a text file containing instructions for building source code.

## Docker Images

Docker images consists of layers. Each layer is the result of a build instruction in the Dockerfile. Layers are stacked sequentially, and each one is a delta representing the changes applied to the previous layer.

The breakdown of a Dockerfile is
- Dockerfile Syntax
- Base Image
- Environment Setup
- Comments
- Installing Dependencies
- Copying Files
- Setting Environment Variables
- Exposed Ports
- Starting the Application

### Dockerfile Syntax

The first line to add is a `#syntax` parser directive. This instructs the Docker builder what syntax to use when parsing the Dockerfile. This should be the first line in Dockerfiles.

```dockerfile
# syntax=docker/dockerfile:1
```

The above always points to the latest release of the version 1 syntax.

### Base Image

The line following the syntax directive defines what base image to use.

```dockerfile
FROM ubuntu:22.04
```

The `FROM` instruction sets the base image to the 22.04 release of Ubuntu. There is a `name:tag` standard for naming Docker images.

### Environment Setup

The following line executes a build command inside the base image

## Building

To build a container image, you use the `docker build` command.

```shell
docker build -t test:latest .
```

The `-t test:latest`  option specifies the name and tag of the image. The single dot at the end of the command sets the build context to the current directory. This means that the build expects to find the Dockerfile in the directory where the command is invoked.

After the image has been built, you can run the application as a container with `docker run`, specifying the image name.

This publishes the container's port 8000 to `http://localhost:8000` on the Docker host.

```shell
docker run -p 127.0.0.1:8000:8000 test:latest
```