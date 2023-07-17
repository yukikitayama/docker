# Docker

- Docker image
  - Universal app packaging
  - `docker build`
- Docker registry
  - Universal app distribution
  - `docker push`
  - `docker pull`
- Docker container
  - Identical runtime environment

Build -> ship -> run

`docker version` verifies CLI can talk to Docker engine

`docker info` shows config values of Docker engine

Docker command line structure
- Old is `docker <command>` e.g. `docker run`
- New is `docker <command> <subcommand>` e.g. `docker container run`

In CLI, no need to type a full container ID. If the first few characters can identify a unique container ID, that's enough

`docker container run --publish 80:80 nginx` means
1. Download nginx image from Docker Hub
2. Start a new container from the image
3. Open the port 80 (left number of :) on the host IP (localhost)
   1. We can use any port number (left number) if 80 is used.
4. Routes the traffic to the container IP (right number of :) port 80

`run` always starts a new container, while `start` starts an existing stopped container.

What happens in the background of `docker container run`
1. Looks for the image locally in image cache
2. If not found, looks for in remote image repository default to Docker Hub
3. Downloads the latest version
4. Create new container based on the image
5. Give virtual IP on a private network inside docker engine
6. Open port 80 on host and forwards to port 80 in container
7. Start container by `CMD` in the image `Dockerfile`

`docker container run -d -p 3306:3306 --name db -e MYSQL_RANDOM_ROOT_PASSWORD=yes  mysql`

`docker container run -d --name webserver -p 8080:80 httpd` can be seen in `localhost:8080`

`docker container run -d --name proxy -p 80:80 nginx` can be seen in `localhost`

`docker ps` is old command. New command is `docker container ls`

`docker container stop CONTAINER_IDs` stops the process

`docker container rm CONTAINER_IDs` deletes the containers

`docker image ls` shows that even if we deleted the containers, the local images still exist.

No SSH needed to interact with a running container. Use docker CLI instead

**Dockerize** is to say that we are making a `Dockerfile` for an existing app or service and we are getting it work
properly in a container.

## Image vs. container

Image is the application we run such as source code

Container is a running instance of the image. So we can have multiple containers running the same image.

## Run containers

- Locally by Docker Desktop, RD
- Servers by Docker Engine, K8s
- PaaS by Cloud Run, Fargate

