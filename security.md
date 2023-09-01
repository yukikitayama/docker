# Security

Client bundle contains a private and public key pair that authorizes your requests in UCP.
- User certificate bundles allows running docker commands through a UCP manager node
- Admin user certificate bundles allows running docker commands on the Docker Engine of any node.

Use **user namespaces** per host to disallow root user

## Control group (cgroup)

Limiting resource usage. By default, Docker can use all the CPU and memory.

Metrics

## Kernel namespaces

Scope

## RBAC

A subject (e.g. user, team) is granted a role for a collection of resources.

- User
- Team - Group of users
- Organization

## Security scanning

Scan images to verify that they are free from known security vulnerabilities or exposure by Docker Security Scanning.

Available in both UCP and DTR

Users with **write access* to a repo can start a security scan. Users with read-only access can view scan results.

To enable Docker Security Scan process,
- Use Docker Enterprise Edition, not Docker Community Edition
- Purchase DTR license that includes Docker Security Scanning and download the license from Docker Hub
- Enable DTR security scanning

## Docker Content Trust (DCT)

Docker Content Trust allows you to verify the authenticity, integrity, and publication date of Docker images that are
made available on the Docker Hub Registry. By default, content trust is disabled, meaning Docker CLI client doesn't
sign the image.

Setting `export DOCKER_CONTENT_TRUST=1` environment variable enables content trust.

Digital signatures for data sent to and received from remote Docker registries allow client-side or runtime verification
of the integrity and publisher of specific image tags.

For signing and verification of image tags

## Secret

In Swarm, after you create a secret, you cannot update it. You can only remove and re-create it, and you cannot remove
a secret that a service is using. Use `--secret-add` and `--secret-rm`

## Docker engine

- Docker engine can be configured to only run signed images by **Docker Content Trust** signature varification. Configured
  in **Dockerd** configuration file.
- Can configure **Secure computing mode (Seccomp)** policies to secure system calls in a container
- Use **certificate-based client-server** authentication to ensure the traffic between Docker registry server and Docker
  daemon (as a client of registry server) is encrypted and authenticated
- Can protect Docker daemon socket and ensure trusted Docker client connections.

## Seccomp

Secure Computing Mode

Security feature of Linux

Restrict the syscalls available to a given process.

## Garbage collection

Out-of-date images may contain security flaws or exploitable vulnerabilities, then garbage collection ensures 
unreferenced images and layers to be removed as Docker security feature.

## Linux

`docker run --cap-drop` can drop Linux capabilities of root in Docker container, so that it has limited access within
the container

https://www.redhat.com/en/blog/secure-your-containers-one-weird-trick

## Universal Control Plane (UCP)

The purpose of UCP is to provide an integrated application management platform

UCP has **role-based access control (RBAC)** to control who can access and make changes to cluster/applications. In UCP,
you can create users and teams, define roles and resource sets of Swarm/Kubernetes and grant them.

UCP integrates with LDAP directory services, so that you can manage users and groups from your organization's directory 
and propagated to UCP.

**Client bundle** contains a private and public key pair to authenticate a user using client certificates to UCP.

Roles in UCP
- None
- View Only
- Restricted Control
- Scheduler
- Full Control

To sign images in a way that UCP trusts the images,
- Configure Notary client
- Initialize trust metadata for the repository
- Delegate signing to the keys in your UCP client bundle

## docker trust

`docker trust` will streamline the image signing process for publishing and managing trusted collections of content.

Notary is an old tool

## Swarm

Each time a new node joins the swarm, the manager issues a certificate to the worker node.

The nodes in a swarm use **mutual Transport Layer Security** to
- Authenticate
- Authorize
- Encrypt the communication with other nodes in the swarm (within the cluster)

**autolock** gives you the ability to protect the mutual TLS encryption key and the key used to encrypt and decrypt secrets,
by allowing you to take ownership of these keys and to require manual unlocking of your managers. It prevents Docker Swarm
encryption keys from being stored insecurely on swarm managers.

`docker swarm update --autolock=true` to enable

## Autolock

Prevent Docker Swarm encryption keys from being stored insecurely on swarm managers

the ability to protect the mutual TLS encryption key and the key used to encrypt and decrypt Raft logs at rest, by 
allowing you to take ownership of these keys and to require manual unlocking of your managers.

Use `--autolock=true` flag with `docker swarm update` command.

When Docker restarts, you must unlock the swarm first, using a key encryption key generated by Docker when the swarm was 
locked. You can rotate this key encryption key at any time.

## Surface of attack

`docker run --cap-drop` reduce the surface of attack from a container

Exclude additional capabilities from being used by processes inside the container on a capability-by-capability basis.

Using `--cap-add` is discouraged.

## RBAC

Docker **labels** enable you to control access to swarm resources by using collections with RBAC model

