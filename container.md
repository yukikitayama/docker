# Container

`docker container ls -a`, `-a` allows us to see the stopped containers.

`docker container rm <container-name>` allows us to deleted the stopped containers.

`docker container rm -f <container-name>` allows us to delete a container which is still running.

To connect to a shell inside of a container, we don't need ssh.

Use `docker container exec -it <container-name> sh (or bash)` command to execute a shell inside of a container.

`--rm` flag means automatically remove the container when it exists.

Typically Docker containers are not built to accept additional parameters when launching 

`docker rm` removes one or more containers, while `docker rmi` removes one or more images.

## Container vs. image

The major difference between container and image is **top writable layer**. All writes to the container are stored in
this writable layer. But when the container is deleted, the writable layer is also deleted. Image remains unchanged.

Multiple containers can share access to the same image but have their own data state.

