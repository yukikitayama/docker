# Persistent Data

Containers are immutable and ephemeral infrastructure.

Persistent data is the databases separate from containers.

## Data volume

Volumes need manual deletion. Removing containers doesn't clean up.

Special location outside of container union file system to store unique data

Preserve across container removal and attach to whatever container

`docker volume prune` to cleanup unused volumes

In Windows, the volume is in Linux VM, so you cannot go to the `Mountpoint` to see the data.

In `mysql`, volume is `/var/lib/mysql`.

`docker run --rm -v <volume-path> <image-name>` makes the volume be removed automatically once the container exists.
Use `-v` or `--volume`.

`docker run --volumes-from=<volume-name>` will copy the mount definitions from one or more containers to the new container.
With this, we can build shared-state relationships in a host-independent way.

### Named volume

Friendly way to assign volumes to containers.

`docker container run -v <volume-name>:<mountpoint>`

`docker volume inspect <volume-name>`

If a container starts with a volume that doesn't exist yet, Docker creates the volume.

## Bind mount

Maps a host file or directory to a container file or directory to make two locations point to the same files.

Host files overwrite anything in container.

You cannot specify in Dockerfile, must be at `container run` by `... run -v //c/Users/somthing:/path/container` (Windows)

Another command is `docker run --mount type=bind,source=<host-path>,target=<path-in-container>`

Working directory

In Windows command prompt, `%cd%`

In Windows PowerShell, `${pwd}`

In Linux, `$(pwd)`

## tmpfs

- `tmpfs` mount type allows data to be stored in the host system's memory.
- When container stops, `tmpfs` mount is removed and the files written there won't be persisted
- For sensitive data that we don't want to persist in the host and container writable layer.
- Best if we don't want the data to persist either on the host machine or within the container.

## Volume vs bind mount vs tmpfs

- Volumes are easier to back up or migrate than bind mounts
- Use `--volumes-from` to create a new container that mounts the volume of backups, restores or migrations
- Volumes are completely managed by Docker, while bind mounts are dependent on the directory structure of the host machine
- https://docs.docker.com/storage/tmpfs/

## Writable layer

Container's writable layer doesn't persist after the container is deleted, but suitable for storing ephemeral data that 
is generated at runtime.

Each container has its own writable container layer.

Multiple containers can share access to the same image and yet have their own data state.

## Copy-on-write (CoW)

1. Search through the image layers for the file to update. The process starts at the newest layer and works down to the 
base layer one layer at a time. When results are found, they are added to a cache to speed future operations.
2. Perform a copy_up operation on the first copy of the file that is found, to copy the file to the container's writable
layer
3. Any modifications are made to this copy of the file and the container cannot see the read-only copy of the file that
exists in the lower layer.

## Storage driver

`docker info` displays the storage driver information.

- Manage contents of image layer and writable container layer
- Each storage driver has its own implementation
- But all drivers use stackable image layers and copy-on-write strategy

`overlay2` is the recommended storage driver. When installing Docker for the first time, `overlay2` is used by default.

## devicemapper

devicemapper driver uses block devices for Docker, operating at block level, not file level.

If we need to use multiple block devices, configure `direct-lvm` mode manually.

**Device mapper** is a kernel-based framework for many advanced volume management technologies on Linux.
- **direct-lvm** is a kind of device mapper we should configure when installing Docker for **production** workloads
- **loop-lvm** is a kind of device mapper for testing

To configure a production-ready devicemapper configuration, use `--storage-opt dm.directlvm_device` Docker daemon option,
specifying a block device.

## docker diff

`docker diff <container>` lists the changes to files or directories on a container's filesystem with following symbols.
- `A` file or directory was added
- `D` file or directory was deleted
- `C` file or directory was changed

## Docker daemon

Docker daemon persist all docker related data in following
- `/var/lib/docker` on Linux
- `C:\ProgramData\docker` on Windows

On Linux distributions,
- `/etc` is typically used for configuration files
- `/var` is usually used for log files, temporary files, databases, and all other data not tied to a specific user.
  - `/var/log` for logs
  - `/var/lib` for databases

