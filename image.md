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

## Build

`docker build -t <tag> .` `.` means build an image in this directory.

## Alpine

Alpine is distribution of Linux, very very small.
