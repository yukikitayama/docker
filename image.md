# Image

In image, we have 
- app binaries
- dependencies
- metadata about the image data and about how to run the image

Image can be as small as one file, or big as a Ubuntu distro

It's not OS.

**App binary** is a file that contains machine code for a computer to execute.

**Tag** is something right of `:` after the image name. `<image-name>:<tag>`.

## Image layer

Images are made up of file system changes and metadata.

Each layer is uniquely identified and only stored once on a host.

This saves storage space on host and transfer time on push and pull.

Container is a single read and write layer on top of image

`docker image history` and `docker image inspect` can show the information.

## Image tag

Tag is a pointer to a specific image commit. It points at image ID. Tag is not quite like a version, not quite like a branch, kind of both.

We tag images for upload to Docker Hub or image repository in AWS/Google Cloud.

How tagging is related to image ID

We can create a new image tag from another existing image, but **image ID** will be the same.

We can have many image tags on the same image ID.

`docker image tag <source-image> <target-image>`

## Push

`docker image push` uploads changed layers to an image registry (default is Hub)

## Dockerfile

It's an instruction about how to build an image.

Each Dockerfile **stanza** (command) forms an image layer. The order of stanzas matters.

Top stanzas in Dockerfile will be bottom layers, and bottom stanzas in Dockerfile will be top layers.

When there's no change in one layer and build again it will use cache. But when one layer has a change, it will build
again and all the layers after that need to rebuild.

So keep the top of the Dockerfile the layers which change less. And the things which change more are at the bottom of 
the Dockerfile.

Use `&&` to chain multiple commands in one stanza to form a layer. It saves time and space.

Use `WORKDIR` instead of `RUN cd` for changing the directory.

`COPY . .` is the easiest way to copy all the files in the host current directory to the image current directory.

Use `#` to comment.

`CMD` is a required parameter. But if you omit, it inherits the `CMD` of the base image but it's not explicit, so good
practice is always to specify `CMD` at the bottom of `Dockerfile`.

Multiline with \ and `&&`

```
RUN apt-get update && apt-get install -y git \
    && do other things
```

## Instruction

`FROM`

Dockerfile must start with FROM. But `ARG` can precede `FROM`. For example,

```
ARG CODE_VERSIONS=latest
FROM base:${CODE_VERSION}
```

`ARG` defines a variable by `docker build --build-arg <key>=<value>`. `ARG` variables are not persisted into the image.

`RUN` executes any commands in a new layer on top of the current image and commits the results.

`ADD` additionally support the following as well as copying files and directories like `COPY` but should use `COPY`
- Fetch remote source files if a URL is specified
- Extract the compressed files.

`EXPOSE`
- Container listens on the specified network ports at runtime
- TCP if the protocol isn't specified but TCP and UDP are available.
- Doesn't automatically publish the port because it's the documentation for builders and users about which ports are intended to be published.
- Use `docker run -p` to actually publish and map ports, because `EXPOSE` doesn't publish the port to external systems.

`WORKDIR`
- If WORKDIR doesn't exist, it will be created
- If relative path, relative to the previous path by WORKDIR
- Can be used multiple times in Dockerfile
- WORKDIR can resolve ENV environment variables previously set
- It impacts containers as well as build

`CMD`

Container execution default

`ENV`
- Environment variables available for the subsequent instructions in the build stage
- Environment variables persist when a container is run from the image.

`HEALTHCHECK`
- Tells Docker how to test a container to check if it's working.
- `HEALTHCHECK CMD` checks container health by running command inside the container.
- `HEALTHCHECK CMD curl localhost:8080` adds health status to detect a web server that is stuck in infinite loop

## Environment variable

Configure Docker CLI by `export DOCKER_CONTENT_TRUST=1` to sign the image that we push to Docker Trusted Registry

## Build context

All the files found in a specific path or URL.

Best to start with an empty directory as context and keep your Dockerfile in that directory. Add only the files needed 
to build the Dockerfile.

`docker build -t <tag> .` `.` means build an image in this directory.

**Docker daemon** runs the build. The first thing a build process does is to send the entire context recursively to 
Docker daemon.

It is not a good practice to use the root directory `/` of your local machine as the PATH for building the docker image. It causes the build
to transfer the entire contents of your hard drive to the Docker daemon.

## Alpine

Alpine is distribution of Linux, very very small.

## docker save

`docker save` saves the image as a TAR file and copy it over to the target host. Then use `docker load` to un-TAR the
image back as a docker image. So the archive can be distributed by file server, version control system, email or flash
drive.
