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

### Named volume

Friendly way to assign volumes to containers.

`docker container run -v <volume-name>:<mountpoint>`

`docker volume inspect <volume-name>`


## Bind mount

Maps a host file or directory to a container file or directory to make two locations point to the same files.

Host files overwrite anything in container.

You cannot specify in Dockerfile, must be at `container run` by `... run -v //c/Users/somthing:/path/container` (Windows)



Working directory

In Windows command prompt, `%cd%`

In Windows PowerShell, `${pwd}`

In Linux, `$(pwd)`


