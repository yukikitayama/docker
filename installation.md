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

When you connect to UCP, the hostname that you use to connect needs to be recognised by UCP's certificates.

Use `--san` Subject Alternative Name (SAN) if you want to use the self-signed certificate when you install UCP.

## Docker daemon

Set configuration options in `/etc/docker/daemon.json` to configure daemon flags and environment variables for Docker
daemon in a platform-independent way

## Logging driver

Configure logging driver in `daemon.json`

`json-file` is the default logging driver

`docker info --format '{{.LoggingDriver}}'` to find the current logging driver

## Backup

