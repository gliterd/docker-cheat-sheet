# Docker Cheat Sheet

## Table of content

* [What is Docker](#what-is-docker)
* [Why Docker](#why-docker)
* [Installation](#installation)
* [Containers](#containers)
* [Images](#images)
* [Networks](#networks)

## What is Docker

"Docker is a tool which helps developers build and ship high quality applications, faster, anywhere."

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
