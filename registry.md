# Registry

Docker Trusted Registry allows deleting images if the image has not been signed.

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

