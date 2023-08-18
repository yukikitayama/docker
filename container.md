# Container

`docker container ls -a`, `-a` allows us to see the stopped containers.

`docker container rm <container-name>` allows us to deleted the stopped containers.

`docker container rm -f <container-name>` allows us to delete a container which is still running.

To connect to a shell inside of a container, we don't need ssh.

Use `docker container exec -it <container-name> sh (or bash)` command to execute a shell inside of a container.

`--rm` flag means automatically remove the container when it exists.

Typically Docker containers are not built to accept additional parameters when launching 

`docker rm` removes one or more containers, while `docker rmi` removes one or more images.

- `--default-limit` allows you to set the default ulimit options to use for all containers
- `--ulimit` in `docker run` will set various restrictions on resource usage such as stack size, open files, and processes.

`docker run -p <host-port>:<container-port>` to make a port available to services outside of Docker

## Namespace

Namespace provides isolation for running processes.

Docker uses namespace to prove the isolated workspace called **container**.

When you run a container, Docker creates a set of namespaces for the container.

## Container vs. image

The major difference between container and image is **top writable layer**. All writes to the container are stored in
this writable layer. But when the container is deleted, the writable layer is also deleted. Image remains unchanged.

Multiple containers can share access to the same image but have their own data state.

## CPU

By default, each container's access to the host machine's CPU cycles is unlimited.

Use `--cpuset-cpus` to configure the amount of access to CPU resources that container has by comma-separated list or 
hyphen-separated range of CPUs. In the list, first CPU is 0. For example,
- `docker run --cpuset-cpus="1,3"` means processes in container can be executed on first and forth CPU.
- `docker run --cpuset-cpus="0-2"` means processes in container can be executed on first, second and third CPU.

## Monitor

Use `docker system events` to get real-time events from the server to interactively monitor all container activity in
the Docker engine.

## Port

`docker run -P` publishes all exposed ports of the container to random ports on the host interfaces.

## Container format

Default container format is libcontainer

Docker Engine combines the followings into a wrapper called container format.
- Namespaces
- Control group
- UnionFS

## DNS

`docker container create --dns=<ip-address>` to create a container with custom DNS server.
