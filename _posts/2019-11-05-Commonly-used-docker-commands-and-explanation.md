---
layout: post
title: "Commonly used docker commands and explanation"
author: afikur
categories: [docker]
image: assets/images/2.jpg
---

In this article we’ll be discussing about few docker command that we need frequently to work with Docker.

### Managing Docker images and Containers

These are the few commands you need frequently to work with docker. But each of them has some options and arguments that might also be needed. We’ll talk about that as well in this article.

| docker run                         | Starts a new container                                                          |
| ---------------------------------- | ------------------------------------------------------------------------------- |
| docker pull                        | Copies/pulls image from default registry if doesn’t already have in docker host |
| docker images                      | List of images on the Docker host                                               |
| docker ps                          | List of running containers                                                      |
| docker ps -a                       | List of all containers                                                          |
| docker stop                        | Stops running containers                                                        |
| docker start                       | Start a stopped container                                                       |
| docker rm                          | Remove stopped containers                                                       |
| docker rmi                         | Remove images                                                                   |
| docker rm -f [container]           | Stops and removes forcefully                                                    |
| docker stop \$(docker ps -a -q)    | Stops all running containers                                                    |
| docker rm \$(docker ps -a -q)      | Removes all stopped container                                                   |
| docker rmi \$(docker images -a -q) | Removes all images from docker host registry                                    |

### docker run

`docker run` command is one of the most used command in docker. We’ve used this command already with some optional flags as well. But let’s talk about some commonly used options for `docker run` command in this article.

#### Usage

```bash
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

#### Options

| –entrypoint          | Overwrite the default ENTRYPOINT of the image                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------------------------- |
| –env , -e            | Set environment variables                                                                                        |
| –health-cmd          | Command to run to check health                                                                                   |
| –health-interval     | Time between running the check (ms\|s\|m\|h) (default 0s)                                                        |
| –health-retries      | Consecutive failures needed to report unhealthy                                                                  |
| –health-start-period | Start period for the container to initialize before starting health-retries countdown (ms\|s\|m\|h) (default 0s) |
| –health-timeout      | Maximum time to allow one check to run (ms\|s\|m\|h) (default 0s)                                                |
| –name                | Assign a name to the container                                                                                   |
| –net                 | Connect a container to a network                                                                                 |
| –publish , -p        | Publish a container’s port(s) to the host                                                                        |
| –volume , -v         | Bind mount a volume                                                                                              |
| –workdir , -w        | Working directory inside the container                                                                           |

### docker stop

### docker restart

### docker build

### docker logs

### docker cp

### docker rm

### docker rmi
