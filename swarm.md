# Swarm

Automate container lifecycle, scale out/in/up/down, re-create if fail, replace containers without downtime (blue/green 
deploy), control/track where containers get started, cross-node virtual network, ensure only trusted servers run containers,
store secrets and get them to the containers.

Swarm mode is a clustering solution built inside Docker.

Not enabled by default, such as `docker swarm`, `docker node`, ...

Single service can have multiple tasks by the specified number of replicas. Each task launches a container

`docker info` to check `swarm: inactive` or not.

`docker swarm init`

`docker node ls`

In swarm, `docker service` replaces `docker run`. `docker run` is a single host idea, but `docker service` is a cluster
idea.

`docker service create alpine ping 8.8.8.8`

`docker service ls`, `docker service ps <service-name>`

`docker service update <service-id> --replicas 3` changes the number of replicas to 3.

`play-with-docker.com`

`docker swarm init --advertise-addr <ip-address>`
