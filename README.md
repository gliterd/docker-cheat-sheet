# Docker Cheat Sheet

## Table of content

* [What is Docker?](#what-is-docker?)
* [Why Docker](#why-docker)
* [Installation](#installation)
* [Containers](#containers)
* [Images](#images)
* [Networks](#networks)
* [Dockerfile](#dockerfile)
* [Layers](#layers)
* [Volumes](#volumes)
* [Links](#links)
* [Exposing Ports](#exposing-ports)
* [Best Practices](#best-practices)
* [Security](#security)
* [Tips](#tips)

## What is Docker?

Docker is a tool which helps developers build and ship high quality applications, faster, anywhere.

## Why Docker

With Docker, developers can build any app in any language using any toolchain. Dockerized apps are completely portable and can run anywhere.

Developers can get going by just spinning any container out of list on Docker Hub. Docker manages and tracks changes and dependencies, making it easier for sysadmins to understand how the apps that developers build work. And with Docker Hub, developers can automate their build pipeline and share artifacts with collaborators through public or private repositories.

If you are a complete Docker newbie, you should probably follow the [series of tutorials](https://docs.docker.com/engine/getstarted/) now.

## Installation

### Linux

Quick and easy install script provided by Docker:

```
curl -sSL https://get.docker.com/ | sh
```

If you're not willing to run a random shell script, please see the [installation](https://docs.docker.com/engine/installation/linux/) instructions for your distribution.


### Mac OS X

Download and install [Docker For Mac](https://docs.docker.com/docker-for-mac/install/)

Then start up a container:

```
docker run hello-world
```

That's it, you have a running Docker container.

If you are a complete Docker newbie, you should probably follow the [series of tutorials](https://docs.docker.com/engine/getstarted/) now.

## Containers

Docker implements a high-level API to provide lightweight containers that run processes in [isolation](http://etherealmind.com/basics-docker-containers-hypervisors-coreos/).

### Lifecycle

* [`docker create`](https://docs.docker.com/engine/reference/commandline/create) creates a container but does not start it.
* [`docker rename`](https://docs.docker.com/engine/reference/commandline/rename/) allows the container to be renamed.
* [`docker run`](https://docs.docker.com/engine/reference/commandline/run) creates and starts a container in one operation.
* [`docker rm`](https://docs.docker.com/engine/reference/commandline/rm) deletes a container.
* [`docker update`](https://docs.docker.com/engine/reference/commandline/update/) updates a container's resource limits.

Normally if you run a container without options it will start and stop immediately, if you want keep it running you can use the command, `docker run -td container_id` this will use the option `-t` that will allocate a pseudo-TTY session and `-d` that will detach automatically the container (run container in background and print container ID)


If you want a transient container, `docker run --rm` will remove the container after it stops.

Another useful option is `docker run --name customname docker_image` because when you specify the `--name` inside the run command this will allow you to start and stop a container by calling it with the name the you specified when you created it.

### Starting and Stopping

* [`docker start`](https://docs.docker.com/engine/reference/commandline/start) starts a container so it is running.
* [`docker stop`](https://docs.docker.com/engine/reference/commandline/stop) stops a running container.
* [`docker restart`](https://docs.docker.com/engine/reference/commandline/restart) stops and starts a container.
* [`docker pause`](https://docs.docker.com/engine/reference/commandline/pause/) pauses a running container, "freezing" it in place.
* [`docker unpause`](https://docs.docker.com/engine/reference/commandline/unpause/) will unpause a running container.
* [`docker wait`](https://docs.docker.com/engine/reference/commandline/wait) blocks until running container stops.
* [`docker kill`](https://docs.docker.com/engine/reference/commandline/kill) sends a SIGKILL to a running container.
* [`docker attach`](https://docs.docker.com/engine/reference/commandline/attach) will connect to a running container.

If you want to integrate a container with a [host process manager](https://docs.docker.com/engine/admin/host_integration/), start the daemon with `-r=false` then use `docker start -a`.

If you want to expose container ports through the host, see the [exposing ports](#exposing-ports) section.

Restart policies on crashed docker instances are [covered here](http://container42.com/2014/09/30/docker-restart-policies/).

### Info

* [`docker ps`](https://docs.docker.com/engine/reference/commandline/ps) shows running containers.
* [`docker logs`](https://docs.docker.com/engine/reference/commandline/logs) gets logs from container.  (You can use a custom log driver, but logs is only available for `json-file` and `journald` in 1.10).
* [`docker inspect`](https://docs.docker.com/engine/reference/commandline/inspect) looks at all the info on a container (including IP address).
* [`docker events`](https://docs.docker.com/engine/reference/commandline/events) gets events from container.
* [`docker port`](https://docs.docker.com/engine/reference/commandline/port) shows public facing port of container.
* [`docker top`](https://docs.docker.com/engine/reference/commandline/top) shows running processes in container.
* [`docker stats`](https://docs.docker.com/engine/reference/commandline/stats) shows containers' resource usage statistics.
* [`docker diff`](https://docs.docker.com/engine/reference/commandline/diff) shows changed files in the container's FS.

`docker ps -a` shows running and stopped containers.

`docker stats --all` shows a running list of containers.

### Import / Export

* [`docker cp`](https://docs.docker.com/engine/reference/commandline/cp) copies files or folders between a container and the local filesystem.
* [`docker export`](https://docs.docker.com/engine/reference/commandline/export) turns container filesystem into tarball archive stream to STDOUT.

### Executing Commands

* [`docker exec`](https://docs.docker.com/engine/reference/commandline/exec) to execute a command in container.

To enter a running container, attach a new shell process to a running container called foo, use: `docker exec -it foo /bin/bash`.

## Images

Images are just [templates for docker containers](https://docs.docker.com/engine/understanding-docker/#how-does-a-docker-image-work).

### Lifecycle

* [`docker images`](https://docs.docker.com/engine/reference/commandline/images) shows all images.
* [`docker import`](https://docs.docker.com/engine/reference/commandline/import) creates an image from a tarball.
* [`docker build`](https://docs.docker.com/engine/reference/commandline/build) creates image from Dockerfile.
* [`docker commit`](https://docs.docker.com/engine/reference/commandline/commit) creates image from a container, pausing it temporarily if it is running.
* [`docker rmi`](https://docs.docker.com/engine/reference/commandline/rmi) removes an image.
* [`docker load`](https://docs.docker.com/engine/reference/commandline/load) loads an image from a tar archive as STDIN, including images and tags (as of 0.7).
* [`docker save`](https://docs.docker.com/engine/reference/commandline/save) saves an image to a tar archive stream to STDOUT with all parent layers, tags & versions (as of 0.7).

### Info

* [`docker history`](https://docs.docker.com/engine/reference/commandline/history) shows history of image.
* [`docker tag`](https://docs.docker.com/engine/reference/commandline/tag) tags an image to a name (local or registry).

### Cleaning up

While you can use the `docker rmi` command to remove specific images, there's a tool called [docker-gc](https://github.com/spotify/docker-gc) that will clean up images that are no longer used by any containers in a safe manner.

### Load/Save image

Load an image from file:
```
docker load < my_image.tar.gz
```

Save an existing image:
```
docker save my_image:my_tag | gzip > my_image.tar.gz
```

### Import/Export container

Import a container as an image from file:
```
cat my_container.tar.gz | docker import - my_image:my_tag
```

Export an existing container:
```
docker export my_container | gzip > my_container.tar.gz
```

### Difference between loading a saved image and importing an exported container as an image

Loading an image using the `load` command creates a new image including its history.  
Importing a container as an image using the `import` command creates a new image excluding the history which results in a smaller image size compared to loading an image.

## Networks

Docker has a [networks](https://docs.docker.com/engine/userguide/networking/) to configure docker containers to talk to each other without using ports. See [working with networks](https://docs.docker.com/engine/userguide/networking/work-with-networks/) for more details.

### Lifecycle

* [`docker network create`](https://docs.docker.com/engine/reference/commandline/network_create/)
* [`docker network rm`](https://docs.docker.com/engine/reference/commandline/network_rm/)

### Info

* [`docker network ls`](https://docs.docker.com/engine/reference/commandline/network_ls/)
* [`docker network inspect`](https://docs.docker.com/engine/reference/commandline/network_inspect/)

### Connection

* [`docker network connect`](https://docs.docker.com/engine/reference/commandline/network_connect/)
* [`docker network disconnect`](https://docs.docker.com/engine/reference/commandline/network_disconnect/)

You can specify a [specific IP address for a container](https://blog.jessfraz.com/post/ips-for-all-the-things/):

```
# create a new bridge network with your subnet and gateway for your ip block
docker network create --subnet 203.0.113.0/24 --gateway 203.0.113.254 iptastic

# run a nginx container with a specific ip in that block
$ docker run --rm -it --net iptastic --ip 203.0.113.2 nginx

# curl the ip from any other place (assuming this is a public ip block duh)
$ curl 203.0.113.2
```

## Registry & Repository

A repository is a *hosted* collection of tagged images that together create the file system for a container.

A registry is a *host* -- a server that stores repositories and provides an HTTP API for [managing the uploading and downloading of repositories](https://docs.docker.com/engine/tutorials/dockerrepos/).

Docker.com hosts its own [index](https://hub.docker.com/) to a central registry which contains a large number of repositories.  Having said that, the central docker registry [does not do a good job of verifying images](https://titanous.com/posts/docker-insecurity) and should be avoided if you're worried about security.

* [`docker login`](https://docs.docker.com/engine/reference/commandline/login) to login to a registry.
* [`docker logout`](https://docs.docker.com/engine/reference/commandline/logout) to logout from a registry.
* [`docker search`](https://docs.docker.com/engine/reference/commandline/search) searches registry for image.
* [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull) pulls an image from registry to local machine.
* [`docker push`](https://docs.docker.com/engine/reference/commandline/push) pushes an image to the registry from local machine.

### Run local registry

You can run a local registry by using the [docker distribution](https://github.com/docker/distribution) project and looking at the [local deploy](https://github.com/docker/docker.github.io/blob/master/registry/deploying.md) instructions.

Also see the [mailing list](https://groups.google.com/a/dockerproject.org/forum/#!forum/distribution).

## Dockerfile

[The configuration file](https://docs.docker.com/engine/reference/builder/). Sets up a Docker container when you run `docker build` on it. Vastly preferable to `docker commit`.  

Here are some common text editors and their syntax highlighting modules you could use to create Dockerfiles:
* If you use [jEdit](http://jedit.org), I've put up a syntax highlighting module for [Dockerfile](https://github.com/wsargent/jedit-docker-mode) you can use.
* [Sublime Text 2](https://packagecontrol.io/packages/Dockerfile%20Syntax%20Highlighting)
* [Atom](https://atom.io/packages/language-docker)
* [Vim](https://github.com/ekalinin/Dockerfile.vim)
* [Emacs](https://github.com/spotify/dockerfile-mode)
* [TextMate](https://github.com/docker/docker/tree/master/contrib/syntax/textmate)
* [VS Code](https://github.com/Microsoft/vscode-docker)
* Also see [Docker meets the IDE](https://domeide.github.io/)

### Instructions

* [.dockerignore](https://docs.docker.com/engine/reference/builder/#dockerignore-file)
* [FROM](https://docs.docker.com/engine/reference/builder/#from) Sets the Base Image for subsequent instructions.
* [MAINTAINER (deprecated - use LABEL instead)](https://docs.docker.com/engine/reference/builder/#maintainer-deprecated) Set the Author field of the generated images.
* [RUN](https://docs.docker.com/engine/reference/builder/#run) execute any commands in a new layer on top of the current image and commit the results.
* [CMD](https://docs.docker.com/engine/reference/builder/#cmd) provide defaults for an executing container.
* [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) informs Docker that the container listens on the specified network ports at runtime.  NOTE: does not actually make ports accessible.
* [ENV](https://docs.docker.com/engine/reference/builder/#env) sets environment variable.
* [ADD](https://docs.docker.com/engine/reference/builder/#add) copies new files, directories or remote file to container.  Invalidates caches. Avoid `ADD` and use `COPY` instead.
* [COPY](https://docs.docker.com/engine/reference/builder/#copy) copies new files or directories to container.  Note that this only copies as root, so you have to chown manually regardless of your USER / WORKDIR setting.  See https://github.com/moby/moby/issues/30110
* [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#entrypoint) configures a container that will run as an executable.
* [VOLUME](https://docs.docker.com/engine/reference/builder/#volume) creates a mount point for externally mounted volumes or other containers.
* [USER](https://docs.docker.com/engine/reference/builder/#user) sets the user name for following RUN / CMD / ENTRYPOINT commands.
* [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir) sets the working directory.
* [ARG](https://docs.docker.com/engine/reference/builder/#arg) defines a build-time variable.
* [ONBUILD](https://docs.docker.com/engine/reference/builder/#onbuild) adds a trigger instruction when the image is used as the base for another build.
* [STOPSIGNAL](https://docs.docker.com/engine/reference/builder/#stopsignal) sets the system call signal that will be sent to the container to exit.
* [LABEL](https://docs.docker.com/engine/userguide/labels-custom-metadata/) apply key/value metadata to your images, containers, or daemons.

### Examples

* [Examples](https://docs.docker.com/engine/reference/builder/#dockerfile-examples)
* [Best practices for writing Dockerfiles](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/)
* [Michael Crosby](http://crosbymichael.com/) has some more [Dockerfiles best practices](http://crosbymichael.com/dockerfile-best-practices.html) / [take 2](http://crosbymichael.com/dockerfile-best-practices-take-2.html).
* [Building Good Docker Images](http://jonathan.bergknoff.com/journal/building-good-docker-images) / [Building Better Docker Images](http://jonathan.bergknoff.com/journal/building-better-docker-images)
* [Managing Container Configuration with Metadata](https://speakerdeck.com/garethr/managing-container-configuration-with-metadata)

## Layers

The versioned filesystem in Docker is based on layers. They're like [git commits or changesets for filesystems](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/).

## Volumes

Docker volumes are [free-floating filesystems](https://docs.docker.com/engine/tutorials/dockervolumes/). They don't have to be connected to a particular container. You should use volumes mounted from [data-only containers](https://medium.com/@ramangupta/why-docker-data-containers-are-good-589b3c6c749e) for portability.  

### Lifecycle

* [`docker volume create`](https://docs.docker.com/engine/reference/commandline/volume_create/)
* [`docker volume rm`](https://docs.docker.com/engine/reference/commandline/volume_rm/)

### Info

* [`docker volume ls`](https://docs.docker.com/engine/reference/commandline/volume_ls/)
* [`docker volume inspect`](https://docs.docker.com/engine/reference/commandline/volume_inspect/)

Volumes are useful in situations where you can't use links (which are TCP/IP only). For instance, if you need to have two docker instances communicate by leaving stuff on the filesystem.

You can mount them in several docker containers at once, using `docker run --volumes-from`.

Because volumes are isolated filesystems, they are often used to store state from computations between transient containers. That is, you can have a stateless and transient container run from a recipe, blow it away, and then have a second instance of the transient container pick up from where the last one left off.

See [advanced volumes](http://crosbymichael.com/advanced-docker-volumes.html) for more details. Container42 is [also helpful](http://container42.com/2014/11/03/docker-indepth-volumes/).

You can [map MacOS host directories as docker volumes](https://docs.docker.com/engine/tutorials/dockervolumes/#mount-a-host-directory-as-a-data-volume):

```
docker run -v /Users/wsargent/myapp/src:/src
```

You can use remote NFS volumes if you're [feeling brave](https://docs.docker.com/engine/tutorials/dockervolumes/#/mount-a-shared-storage-volume-as-a-data-volume).

You may also consider running data-only containers as described [here](http://container42.com/2013/12/16/persistent-volumes-with-docker-container-as-volume-pattern/) to provide some data portability.

## Links

Links are how Docker containers talk to each other [through TCP/IP ports](https://docs.docker.com/engine/userguide/networking/default_network/dockerlinks/). [Linking into Redis](https://docs.docker.com/engine/examples/running_redis_service/) and [Atlassian](https://blogs.atlassian.com/2013/11/docker-all-the-things-at-atlassian-automation-and-wiring/) show worked examples. You can also resolve [links by hostname](https://docs.docker.com/engine/userguide/networking/default_network/dockerlinks/#/updating-the-etchosts-file).

This has been deprected to some extent by [user-defined networks](https://docs.docker.com/engine/userguide/networking/#user-defined-networks).

NOTE: If you want containers to ONLY communicate with each other through links, start the docker daemon with `-icc=false` to disable inter process communication.

If you have a container with the name CONTAINER (specified by `docker run --name CONTAINER`) and in the Dockerfile, it has an exposed port:

```
EXPOSE 1337
```

Then if we create another container called LINKED like so:

```
docker run -d --link CONTAINER:ALIAS --name LINKED user/wordpress
```

Then the exposed ports and aliases of CONTAINER will show up in LINKED with the following environment variables:

```
$ALIAS_PORT_1337_TCP_PORT
$ALIAS_PORT_1337_TCP_ADDR
```

And you can connect to it that way.

To delete links, use `docker rm --link`.

Generally, linking between docker services is a subset of "service discovery", a big problem if you're planning to use Docker at scale in production.  Please read [The Docker Ecosystem: Service Discovery and Distributed Configuration Stores](https://www.digitalocean.com/community/tutorials/the-docker-ecosystem-service-discovery-and-distributed-configuration-stores) for more info.
