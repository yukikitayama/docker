# Security

Client bundle contains a private and public key pair that authorizes your requests in UCP.
- User certificate bundles allows running docker commands through a UCP manager node
- Admin user certificate bundles allows running docker commands on the Docker Engine of any node.

## Security scanning

Scan images to verify that they are free from known security vulnerabilities or exposure by Docker Security Scanning.

Available in both UCP and DTR

## Docker Content Trust (DCT)

Digital signatures for data sent to and received from remote Docker registries allow client-side or runtime verification
of the integrity and publisher of specific image tags.

## Secret

In Swarm, after you create a secret, you cannot update it. You can only remove and re-create it, and you cannot remove
a secret that a service is using. Use `--secret-add` and `--secret-rm`


