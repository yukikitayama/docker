# Cleanup

Save local space

`docker system df` checks how much local disk spaces are used by Docker.

`docker image prune` deletes the non-latest images and keep the latest tag images for each repository. This cleans up 
**dangling** images, meaning layers that are not important to the latest images.

`docker image prune -a` deletes all the local images including latest tags.

`docker system prune` will clean up everything that we are not currently using.

https://www.youtube.com/watch?v=_4QzP7uwtvI
