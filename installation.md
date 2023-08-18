# Installation

## Docker Enterprise

Docker Enterprise has 3 components for image creation, securing image storage, securing image deployment.

- Docker Engine Enterprise
  - Commercially supported Docker Engine for creating images and running them in Docker containers
- Docker Trusted Registry (DTR)
  - Production-grade image storage
- Universal Control Plane (UCP)
  - Deploys applications from images by managing orchestrators like Kubernetes and Swarm

## Docker Trusted Registry (DTR)

Installation requirements
- DTR can be installed on-premises or on a cloud provider
- All nodes must have fixed hostname
- All nodes must be a worker node managed by UCP

`dtr-ol` allows DTR components running on different nodes to communicate to replicate DTR data. Overlay network.

**DTR cache** to decrease the time to pull images for geographically dispersed users

## Universal Control Plane (UCP)

Docker Universal Control Plane is the enterprise-grade cluster management solution. UCP extends the functionality
provided by Docker to make it easier to manage your cluster from a centralized place to provide an integrated
application management platform.

When you connect to UCP, the hostname that you use to connect needs to be recognised by UCP's certificates.

Use `--san` Subject Alternative Name (SAN) if you want to use the self-signed certificate when you install UCP.

## Docker daemon

Set configuration options in `/etc/docker/daemon.json` to configure daemon flags and environment variables for Docker
daemon in a platform-independent way

To set the Docker engine into debug mode, `echo '{"debug": true}' > /etc/docker/daemon.json; sudo kill -HUP`.

To configure Docker daemon to start as the system boots up
- Use **systemd** for most Linux distributions
- Use **upstart** for Ubuntu

`sudo systemctl enable docker` to automatically start Docker and containerd on boot for other Linux distributions than
Debian and Ubuntu using `systemd`.

With **live restore**, we can configure daemon so that containers remain running if the daemon becomes unavailable.
By default, when Docker daemon terminates, it shuts down running containers. But live restore reduce container downtime.

Use `dockerd` to manually run docker daemon for testing, and it runs in the foreground and sends its logs to terminal.

## Docker engine

The recommended way to install Docker engine on a production server is to install using **repository**

## Logging driver

Configure logging driver in `daemon.json`. Set log-driver option in `daemon.json` to configure the default logging
for containers.

`json-file` is the default logging driver

`docker info --format '{{.LoggingDriver}}'` to find the current logging driver

`docker inspect <container-name>` to find the current logging driver for a running container.

## Backup

To back up Docker Enterprise, create following backups
- Docker Swarm like service and network definitions
- UCP like access control and certificates
- DTR like images and metadata.

To back up Universal Control Plane (UCP) metadata, run a container form the UCP image with the backup command.

Use `docker/ucp backup` in `docker run` to back up UCP

## Debug

To start Docker in debug mode, set debug key to true in `daemon.json` in `etc/docker/`

## Health check

Use `https://<ucp-manager-url>/_ping` endpoint to check the health of a single UCP manager node.

## Control group

Docker Engine relies on control group **cgroup**.

cgroup limits an application to a specific set of resources. For example, limit the memory or CPU available to a 
specific container.

The following options for resource limitation is representative of a control group (cgroup) when added to a container.
- `--cpus=[value]` to set max available CPU that a container can use
- `--memory=[amount b/k/m/g]` to set max available memory that a container can use
