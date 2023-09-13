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

`docker swarm join-token manager` to get a toke to add a new node to swarm

`10.0.0.0/8` is a default address pool that Docker Swarm uses by default for global scope networks.

Order of steps taken when creating a service process in swarm mode. `AO Ads` to remember
- API
  - Creates service objects
- Orchestrator
  - Manage states of tasks
- Allocator
  - Allocates IP address to a task
- Dispatcher
  - Determines on which node a task will be scheduled
- Scheduler
  - Instructs a worker node to run a task

## Networking

**Overlay** is the swarm-wide bridge network driver where containers can communicate across nodes.

Use `--driver overlay` in creating network for container-to-container traffic within a single Swarm

Each service can be connected to multiple networks.

## docker service

`docker run` is a single host solution. `docker service` is equivalent to `run` in swarm.

`docker service create` creates a service

`docker service ls` to list services

`docker service ps <service-name>` shows actual tasks or containers for that service.

`docker service update` updates without taking down the services

`docker service rm <service-name>` to take down the service

## Manager

There is no limit on the number of manager nodes.

Adding manager nodes to a swarm makes the swarm more **fault-tolerant**.

Additional manager nodes reduce the write **performance**, because more nodes must acknowledge proposals to update the swarm
state.

Run `docker swarm join-token worker` on a manager node, to retrieve the join token for worker nodes

## Worker

Run `docker swarm join --token <token>` on a worker node to add a worker to the swarm

## Task

A unit of services in Swarm

A task advance through a number of states until they complete or fail.

`NEW` state si the state where a task is initialized.

Task only progresses forward states, and doesn't go backward.

## Node

Node is an instance of the **Docker engine** participating in the swarm, as a Docker node.

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
Configure service to run only on nodes with specific **metadata**.
`docker service update --constraints-add`

**Placement preference** try to place tasks on appropriate nodes in algorithmic way. 
`docker service update --placement-pref-add`

## Service update

Provides rolling replacement of tasks/containers in a service, limits downtime, will replace containers for most 
changes, use `-add` or `-rm`, includes rollback and health check options

For example,
- `docker service update --env-add ENV=prod --publish-rm 8080` to add environment variable and to remove port
- `docker service scale service-a=2 service-b=3` to change replicas

## Docker healthchecks

Docker engine will check a basic health status inside the container

It expects exit 0 for OK or exit 1 for Error

3 container states
- starting
- healthy
- unhealthy

`docker container ls` shows healthcheck status

`docker container inspect` shows last 5 healthchecks

`HEALTHCHECK --options` in Dockerfile

Services will replace tasks if they fail healthcheck

Service updates wait for them before continuing

## Replicated service vs. global service

Swarm mode has 2 types of services; replicated and global. Control by `--mode`. Default is replicated service

A number of tasks can be specified for a replicated service, but there's no pre-specified number of tasks for global service.

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

Setting a node to `DRAIN` doesn't remove standalone containers from the node, because drain affects the node's ability
to schedule swarm service workloads.

## Pending

The services remain pending when
- The amount of memory reserved for a service not satisfied
- All nodes are paused or drained
- Imposed placement constraints not honored (?)

## Service discovery

The mechanism Docker uses to route a request from external client to an individual swarm node.

- Virtual IP (VIP) with `--endpoint-mode vip`
- DNS round-robin with `--endpoint-mode dnsrr`

## Routing mesh

Routes ingress (incoming) packets for a service to a proper task to **load balance** Swarm services across their tasks.

Spans all nodes in Swarm

Uses IPVS from Linux Kernel

- Container-to-container in a **overlay** network using virtual IP VIP
- External traffic incoming to published ports and all nodes listen to

It's stateless load balancing, so if you need to use session cookies on your applications, or if it expects a consistent
container to talk to, you may need to add other things to solve that problem.

This load balancer is OSI Layer 3 (TCP), not Layer 4 (DNS).

Docker Enterprise Edition comes with L4 web proxy.

## Multiple nodes swarm cluster

In play with docker, `ctrl + insert` to copy, `shift + insert` to paste
https://github.com/play-with-docker/play-with-docker/issues/65

Initialize swarm by `docker swarm init --advertise-addr <ip-address>` use IP address accessible from other servers.

Copy swarm join command and go to other nodes

`docker node update --role manager <node>` to assing manager

`docker swarm join-toke manager` to retrieve token

## Stack

Production-grade `compose`, stacks of services.

Stacks accept compose files as their declarative definition for services, networks, and volumes.

Use `docker stack deploy` to create

Use `deploy:` key in compose file, no `build:`, so compose ignores `deploy:` and swarm ignores `build:`

`docker stack deploy -c <compose-file-name> <stack-name>` to create Swarm Stack

`docker stack services <stack-name>` to see each service and replica and `docker stack ps <stack-name>` to deep dive for
image name and node

To update an existing stack, update compose YAML file and run again `docker stack deploy -c <compose-file-name> <stack-name>`

## Secret storage

Easiest secure solution for storing secrets in Swarm. Encrypted in disk and encrypted in transit. Doesn't require apps
to be rewritten in order to use secret storage.

Secrets are first stored in Swarm, then assigned to a service. Only containers in assigned services can see them.

Secrets look like files in containers but are actually in-memory filesystem

Secret examples
- Username and password
- TLS certificates and keys
- SSH keys

`docker secret create <secret-name> <file-name-containing-secret>`

`echo "secret-value" | docker secret create <secret-name> -` to make secret in command, the last `-` is stdin.

`docker secret ls` to see secret names. `docker secret inspect <secret-name>` can give us the details but don't give us
the actual secret values.

When you use secret in creating service with `docker service create`, use `--secret <secret-name>` to connect secret
to a service and `/run/secrets/<secret-name>` to use the secret values.

To remove secrets, use `docker service update --secret-rm`, but it redeploys containers.

## Network port

In Swarm mode, the following ports need to be open to traffic to and from each Docker host participating on an overlay 
network

- **TCP port 2377** for cluster management communications
- **TCP and UDP port 7946** for communication among nodes
- **UDP port 4789** for overlay network traffic.

## Security

`docker swarm init --external-ca` specifies your own externally-generated root CA

## Rolling update

`docker service update --update-delay 30s` sets 30 seconds delay between tasks so that rolling restart happens gradually.

## Rollback

`docker service update --rollback <service-name>` or `docker service rollback <service-name>` to rollback a service to
its previous version

