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

`docker network connect <network-name> <container-name>` attaches a network to a container

`docker network disconnect` detaches a network from a container

`docker network create -d overlay --attachable <network-name>`, `--attachable` creates an overlay network for standalone
containers or swarm services to communicate with other standalone containers.

## Routing mesh

Services using the routing mesh are running in **virtual IP (VIP)** mode.

When using the routing mesh, there is no guarantee about which Docker node services client requests.

## Host network

Host network connects the network stack of the container directly to Docker Host. Container's network stack is not
isolated from the Docker host. The container doesn't get its own IP address allocated.

## Ingress

Overlay network to handle control and data traffic related to swarm services.

Ingress is one of the types of overlay networks. It sits on top of the host-specific networks and handles control and
data traffic related to swarm services.

When you create a swarm service and do not connect it to a user-defined overlay network, it connects to the ingress network
by default.

If your service can be accessed using **HTTP/HTTPS** then **ingress** is recommended.

## docker_gwbridge

A bridge network to connect the individual Docker daemon to the other daemons participating in the swarm

## NodePort

Suited for application services that require direct TCP/UDP access, like RabbitMQ for example.

## DNS

Docker deamon has a built-in DNS server that containers use by default.

Docker defaults the hostname to the container's name, but we can also set aliases.

Hostname and container name are the same

Containers shouldn't rely on IP's for inter-communication

DNS for friendly names is built-in if you use custom networks, it's recommended to always create custom networks.

## Overlay network

Best when
- You need containers running on different Docker hosts to communicate
- Multiple applications work together using swarm services.

`docker network create --driver overlay <network-name>`

All swarm service management traffic is encrypted by default, but to encrypt application data as well, use
`docker network create -d overlay -o encrypted=true` or `--opt`

## User-defined bridge network

- Best when you need multiple containers to communicate on the same Docker host.
- Provide automatic DNS resolution between containers
- Provide better isolation
- Containers can be attached and detached on the fly
- Provide a scoped network in which only containers attached to that network are able to communicate
- Can use **automatic service discover** which allows containers not only to communicate by IP address, but also to resolve
  a container name to an IP address.

## Macvlan network

Best when you are migrating from a VM setup or need your containers to look like physical hosts on your network, each
with a unique MAC address.

The modes that can create Macvlan networks
- Bridge mode - Traffic goes through physical device on the host
- 802.1q trunk bridge mode - Can control routing and filtering at a more granular level.

## Network scope

- Local scope
  - Provide connectivity and network services (such as DNS or IPAM) within the scope of the host.
  - Have unique network ID on each host
- Swarm scope
  - Provide connectivity and network services across a swarm cluster.
  - Have the same network ID across the entire cluster.

## IPAM drivers

IP Address Management drivers

Used to track IP addresses and provide default subnets or IP addresses for the networks and endpoints.

## Sandbox

Perfectly isolates a container from the outside world. No inbound network connection is allowed into the sandbox container.

## Endpoint

Controlled gateway to connect the sandbox that shields the container to the outside world container.

## Virtual IP (VIP)

Virtual IP is internal load balancing

When services are created in a Docker swarm cluster, they are automatically assigned a VIP that is part of the service's
network.

The VIP is returned when resolving the service's name

Traffic to the VIP is automatically sent to all healthy tasks of that service across the overlay network. Client-side
doesn't need load balancing. Docker distributes the traffic across the healthy service tasks.

## Host network mode (Host networking)

Container's network stack is not isolated from Docker host, and the container doesn't get IP address allocated. For 
example, when running container with port 80 and host networking, the container is available on port 80 on the host's
IP address.

Only works on Docker for Linux Distributions hosts.

Useful to optimize performance, handle a large range of ports, without network address translation.

## VXLAN

In data centers, VXLAN is the most commonly used protocol to crete overlay networks that sit on top fo the physical network,
enabling the use of virtual networks.

**overlay** network driver uses VXLAN data plane to decouple docker network from the underlay physical network.

## Service discovery

Docker uses embedded DNS to provide service discovery

## Container network model

Container network model (CNM) is a standard proposed by Docker.

- Sandbox - Isolated environment that contains the container's network configuration. Works as a networking stack within
  the container
- Endpoint - Joins a Sandbox to a network. 
- Network - Uniquely identifiable collection of endpoints allowed to communicate with each other.

