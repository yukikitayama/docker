# Network

By default, each container is connected to a private virtual network `bridge`. Bridge is a default Docker virtual
network which is NAT'ed behind the host IP. When we use the default `bridge` network driver, multiple containers can
communicate with each other on the same docker host.

`host` is a special network that skips virtual networking of Docker and attach containers directly to host interface.

`none` is like having computer interface which isn't attached to anything.

Network driver is a built-in or 3rd party extensions that give you the virtual network features.

Each virtual network routes through NAT firewall on host IP.

All containers on a virtual network can talk to each other without `-p`.

Best practice is to create a new virtual network for each app.

We can attach a container to more than one virtual network

`-p` or `--publish` has `HOST:CONTAINER` format. Left side host port forwards traffic to the right side port of a container.

`docker container port <container-name>`

`docker container inspect <container-name>`

`docker network ls` shows networks

`docker network inspect` inspects a net work

`docker network create <network-name>` create a network

`docker network connect` attaches a network to a container

`docker network disconnect` detaches a network from a container

## DNS

Docker deamon has a built-in DNS server that containers use by default.

Docker defaults the hostname to the container's name, but we can also set aliases.

Hostname and container name are the same

Containers shouldn't rely on IP's for inter-communication

DNS for friendly names is built-in if you use custom networks, it's recommended to always create custom networks.








