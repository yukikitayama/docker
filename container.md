# Container

`docker container ls -a`, `-a` allows us to see the stopped containers.

`docker container rm <container-name>` allows us to deleted the stopped containers.

`docker container rm -f <container-name>` allows us to delete a container which is still running.

To connect to a shell inside of a container, we don't need ssh.

Use `docker container exec -it <container-name> sh (or bash)` command to execute a shell inside of a container.

`--rm` flag means automatically remove the container when it exists.

