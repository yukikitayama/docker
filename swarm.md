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

`docker swarm leave` to remove a node when we run this command on a worker.

`docker service inspect --pretty` to make output in a human-readable format

## docker service

`docker run` is a single host solution. `docker service` is equivalent to `run` in swarm.

`docker service ls` to list services

`docker service ps <service-name>` shows actual tasks or containers for that service.

`docker service update` updates without taking down the services

`docker service rm <service-name>` to take down the service

## Manager

There is no limit on the number of manager nodes.

Adding manager nodes to a swarm makes the swarm more fault-tolerant.

Additional manager nodes reduce the write performance, because more nodes must acknowledge proposals to update the swarm
state.

## Dispatcher

Determines on which node a task will be scheduled. We submit a service definition to a manager node. Manager node dispatches
units of work called tasks to worker nodes. This is how an application is deployed to a swarm.

## Raft consensus algorithm

Raft is the protocol that ensures consistency across multiple nodes.

It's the algorithm that manager nodes implement to manage the global cluster state when the Docker Engine runs in swarm mode.

**Quorum** is a majority of manager nodes

Raft requires the quorum to agree on proposed updates to the swarm such as node additions or removals. If the swarm loses
the quorum of managers, the swarm cannot perform administrative tasks such as scaling and updating. But nevertheless, 
existing tasks continue to run.

Raft tolerates up to `(N - 1) / 2` failures where `N` is number of manager nodes.

Raft requires a majority or quorum of `(N / 2) + 1`. For example, when we have a cluster of 5 manager nodes, `(5 / 2) + 1
= 3.5` manager nodes are required to perform administrative tasks. If 3 nodes are unavailable, the system cannot process requests.

## Service placement

**Placement constraints** limit the nodes a service can run on.

**Placement preference** try to place tasks on appropriate nodes in algorithmic way. `docker service update --placement-pref-add
`

## Replicated service vs. global service

Swarm mode has 2 types of services; replicated and global. Control by `--mode`. Default is replicated service

For a replicated service, 
- we can specify the number of replica tasks for the swarm manager to schedule onto available nodes.
- `docker service scale` command can be used to scale up or down to the desired number of replicas.

For global service, 
- the scheduler places one task on each available node. We cannot pre-specify number of tasks. 
- `docker service scale` command cannot be used
- `--mode global`
- Make sure that only one task is deployed on each active node in the swarm cluster.

## Drain

Prevents a node from receiving new tasks from the swarm manager.

Making a manager node drain means it only performs swarm management tasks, not for task assignment

Making a node drain means we take it down for maintenance.

## Pending

The services remain pending when
- The amount of memory reserved for a service not satisfied
- All nodes are paused or drained
- Imposed placement constraints not honored (?)
