# Compose

Configure relationships between containers

Save docker container run settings in a file

Create one-liner developer environment setups

In `YAML` file, specify containers, networks, and volumes

`docker-compose` or `docker compose` used for local dev/test automation with the YAML files.

`docker-compose.yml` is default name

Compose YAML file has versions

Docker direction used in production with Swarm

`docker compose up` sets up volumes, networks and start all containers

If a project has `Dockerfile` and `docker-compose.yml`, we should do `git clone github.com/repo` and `docker compose up`

## Cleanup

`docker compose down` stops all containers and removes containers, volumes and networks.

`docker compose down -v` removes volumes as well. Be default, Docker protests volumes.

## Build

`docker compose down --rmi local` deletes images too

`build context` in a compose file is meant to specify where the Dockerfile of the image is supposed to build from.

## Scale

To scale an app,
- Change the replicas value in `docker-compose.yml`
- Execute `docker stack deploy`
