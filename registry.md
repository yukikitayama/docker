# Registry

Docker Trusted Registry allows deleting images if the image has not been signed.

`docker container run -d -p 5000:5000 --name registry registry`

`docker tag <image-name> 127.0.0.1:500/<image-name>`

`docker push 127.0.0.1:5000/<image-name>`

`docker pull 127.0.0.1:5000/<image-name>`

## Provider

- Docker Hub
- Docker Enterprise Edition Docker Trusted Registry (DTR)
- Docker Registry
- AWS ECR
- Google Cloud Artifact Registry

## Swarm

Because of Routing Mesh, all nodes can see `127.0.0.1:5000`

All nodes must be able to access images

`docker service create --name registry --publish 5000:5000 registry`

`docker tag <image-name> 127.0.0.1:5000/<image-name>`

`docker push 127.0.0.1:5000/<image-name>`

`docker service create --name <service-name> 127.0.0.1:5000/<image-name>`

## Docker Hub

Most popular public image registry

Docker Registry plus lightweight image building

**Repository Links** allows your image in Docker Hub to be automatically rebuilt when the `FROM` base image is updated.

## Docker Registry

A private image registry for your network.

De facto in private container registries.

Not as fully featured as Hub or others, no web UI, basic auth only.

It's web API and storage system.

## Docker Trusted Registry (DTR)

Docker Trusted Registry (DTR) is the enterprise-grade image storage solution from Docker Enterprise Edition.

By default, users with read and write access to a repository can push the same tag multiple times to that repository.
To prevent an image from being overwritten by another user, use DTR web UI to make the tag immutable.

## Garbage collection

DTP garbage collection is a process of removing blobs from the filesystem when they are no longer referenced by a manifest.
Blobs can include layers and manifests.

Garbage collection removes unreferenced image layers from DTR's backend storage.

## Endpoint

DTR endpoint to assess the health of DTR
- `/health` is useful for load balancing or other automated health check tasks
- `/nginx_status` returns number of connections to DTR frontend NGINX
- `/api/v0/meta/cluster_status` returns DTP replicas information
- `/load_balancer_status` is useful for an administrator to gauge the status of DTR replica

