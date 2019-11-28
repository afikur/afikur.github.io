---
layout: post
title: "Create your first Docker Image | Dockerfile"
author: afikur
categories: [docker]
image: assets/images/8.jpg
---

So, far we’ve used all the images from docker hub. In this article we’ll create our first docker image. For creating an **image**, we need something called **Dockerfile**. A Dockerfile is a text document that contains all the commands a user could call on the command line to assemble an image.

In this article [Docker for Java Developers | Running a simple Java App](http://docker for java developers | running a simple java app/), you’ve already seen that how we can use docker for compiling and running our java application. But we had to to few command for assembling our applicatoin to get the result. Docker file is that instruction written in a text file. Let’s start by an example

### Dockerfile

```docker
FROM openjdk:8

# set work directory
WORKDIR /src

# copy src folder to container's workdir
COPY $PWD/src/ .

# compiling App.java
RUN javac App.java

# executing our program
CMD [ "java", "App" ]
```

Starting with hash(#) means, this is a comment. Docker will ignore these lines when you’ll build this image.

First line: `FROM openjdk:8` is the base image. Every docker file needs a base image. We’ll talk about base image later in this course, but for now just know that we need a base image at the top. We’re using openjdk:8 base image. So in this image we can expect that JDK will be installed.

Second line: `WORKDIR /src` is setting /src the working directory inside the container. I’ve already talked about working directory earlier in this course.

Third line: `COPY $PWD/src/ .` means, copy everything in our src directory to docker working directory.

Fourth line: `RUN javac App.java` means Execute/Run this command. In this case it will compile our java code.

Fifth line: `CMD [ "java", "App" ]` sets default command which can be overwritten from command line when docker container runs. In this case the default command is `java App`. So it will run our application.

**Note:** There is one more instruction of docker called `ENTRYPOINT`. If you want to learn the differences between RUN, CMD and ENTRYPOINT go to [this link](https://goinbigdata.com/docker-run-vs-cmd-vs-entrypoint/).

### Build A Docker Image

For building the image run this command in your terminal

```bash
docker build -t java-test .
docker build` is the instruction to build the image, then with `-t` flag we are saying build the image with the tag java-test. Docker will put latest tag in front of java-test, so the image name will be `java-test:latest
```

Then a dot(.). This specifies that the `PATH` is `.`The `PATH` specifies where to find the files for the “context” of the build on the Docker daemon.

Now if you see the list of images by `docker images` command, you’ll see a brand new image named `java-test` there.