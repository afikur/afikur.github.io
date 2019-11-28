---
layout: post
title: "Run your first docker container"
author: afikur
categories: [docker]
image: assets/images/3.jpg
---

So far, we tried to learn why do we need docker and what’s a docker container. In this lesson we’ll run our first docker container

From your terminal run the following command

```bash
docker run hello-world
```

This command is actually telling docker to run a container from “hello-world” **image**. Container is a running instance of an image. Don’t worry too much about **image** and **container** for now. We’ll discuss more about containers and images later in this series. Just think high level here, by this command we’re expecting a running container.

If you installed docker successfully you’ll see something like this in your terminal.

![docker run hello-world](https://firebasestorage.googleapis.com/v0/b/afikurcom.appspot.com/o/hello-world-docker-response.png?alt=media&token=03cebed3-e0f1-41d5-bdf3-decf8d361d82)docker run hello-world

Let’s start explaining each line

Unable to find image ‘hello-world:latest’ locally

This mean the hello-world image is not available in **docker host local image store**. Don’t worry much about this fancy term. We’ll talk about **docker host local image store** later in this lesson, for now just think that this image is not available locally in your machine.

One more thing to look carefully. We gave the command `docker run hello-world`. But look closely it’s trying to find the image `hello-world:latest`. That is because if you don’t put anything after colon(:) of the name, docker will put latest by default. It’s called **tag**. We can specify tag name if we want. We’ll see more of that later.

latest: Pulling from library/hello-world

Docker is going to pull **latest** tagged image from **[docker hub](https://hub.docker.com/)**. Docker hub is the place where all the images are stored. It’s called docker registry. Docker’s official registry is known as docker hub. But we can create our own docker registry if we want. But we’ll see that later. So, this line means docker is pulling the image from registry, in this case from docker hub.

1b930d010525: Pull complete

This is a layer of an image. An image can be build with one or more layers. For this case this is the only layer of “hello-world” image. But there could be multi-layered image. We’ll see multi-layered image later. This line simply means: layer(1b930d010525) pull has been completed.

Digest: sha256:c3b4ada4687bbaa170745b3e4dd8ac3f194ca95b2d0518b417fb47e5879d9b5f

For now lets’ just ignore the Digest with this long sha256 encoded hash. We’ll talk about this line in some other docker course, where we’ll discuss about docker manifest and everything. Once I finish up writing that article, I’ll attach the link here. But now if you’re interested enough to learn more about this digest, just Google for it.

Status: Downloaded newer image for hello-world:latest

This line showing the status that the image has been download successfully.

After this line everything is actually the output of our newly created container. This hello-world image is nothing but a C program. This below code has been executed when our container ran.

Isn’t it interesting? You didn’t have to know C programming or didn’t have to know how to run a C program or what is the dependency to run a C program at all. You just know how to run a docker container and hallelujah you can run anything in docker. Isn’t it just great?

```c
//#include <unistd.h>
#include <sys/syscall.h>

#ifndef DOCKER_IMAGE
	#define DOCKER_IMAGE "hello-world"
#endif

#ifndef DOCKER_GREETING
	#define DOCKER_GREETING "Hello from Docker!"
#endif

#ifndef DOCKER_ARCH
	#define DOCKER_ARCH "amd64"
#endif

const char message[] =
	"\n"
	DOCKER_GREETING "\n"
	"This message shows that your installation appears to be working correctly.\n"
	"\n"
	"To generate this message, Docker took the following steps:\n"
	" 1. The Docker client contacted the Docker daemon.\n"
	" 2. The Docker daemon pulled the \"" DOCKER_IMAGE "\" image from the Docker Hub.\n"
	"    (" DOCKER_ARCH ")\n"
	" 3. The Docker daemon created a new container from that image which runs the\n"
	"    executable that produces the output you are currently reading.\n"
	" 4. The Docker daemon streamed that output to the Docker client, which sent it\n"
	"    to your terminal.\n"
	"\n"
	"To try something more ambitious, you can run an Ubuntu container with:\n"
	" $ docker run -it ubuntu bash\n"
	"\n"
	"Share images, automate workflows, and more with a free Docker ID:\n"
	" https://hub.docker.com/\n"
	"\n"
	"For more examples and ideas, visit:\n"
	" https://docs.docker.com/get-started/\n"
	"\n";

void _start() {
	//write(1, message, sizeof(message) - 1);
	syscall(SYS_write, 1, message, sizeof(message) - 1);

	//_exit(0);
	syscall(SYS_exit, 0);
}
```

This code is taken from the hello-world repository from GitHub. Here is the GitHub [URL](https://github.com/docker-library/hello-world/blob/master/hello.c) for this code.

This container doesn’t do anything useful. It just a hello-world image what else could you expect from it anyway. But running this image we’ve learned few key features that will be helpful for our docker journey. So, in the next lesson, let’s run something useful.

