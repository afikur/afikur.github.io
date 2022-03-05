---
layout: post
title: "How we do it: Docker for Java Developers"
author: afikur
categories: [docker, java]
tags: [featured]
image: assets/images/docker2.jpg
---

#  How we do it: Docker for Java Developers


Before starting, I want to let you know that this article is not focused on very basics of containerization  technologies like [docker](https://www.docker.com/), [podman](https://podman.io/), [containerd](https://containerd.io/) etc. If you're an absolute beginner just take a course on introductory docker. There are tons of articles and video tutorials available online to learn the basics of docker.



If you already know basics of docker but wondering -

> **What's the best process to follow and how people use docker in the real world?**



In this article I'll demonstrate how my team and I use docker in our daily life to deploy mission critical production grade application.



To deploy a container image to production server there are basically four main stages.

1. Write code 
2. Build docker container image
3. Push docker image to docker registry (cloud/self-hosted)
4. Deploy the container to production server



### 1. Write Code (and run on you local machine)

Some people like to keep their development machine clean and write code to develop application only using docker. That means, your development machine won't contain anything like Java, maven/grade, tomcat etc. You write the code and you keep a container running, all the changes you make that appears in the container and the container has all the necessary tools to build your code and run it. 

But, some people including me don't like this, so I moved from this strategy. I like I like to keep my development environment as it is. But if you're someone who like to use docker while developing the application you can do so. But be aware, you might need to learn how to integrate the workflow with your favorite IDE. A simple example would be running the application in debugging mode won't just help you to debug. You need to enable remote debugging if you run the source code inside a docker container. 

My team use their development machine to develop the application locally, once the development part is done then we containerize the source code to deploy it over QA/Stage/Production environment. So the important thing to remember is -

> **You don't need do develop inside docker to deploy in docker**



Most of the time to develop your application, you need external dependencies installed on your machine like a database server for instance. That's where you can really utilize the power of docker. Let's say you require a mongodb, activemq and a storage server to develop you code. You can run those external dependency in docker containers. I use  [docker-compose](https://docs.docker.com/compose/gettingstarted/) in this situation  where multiple containers need to be run in my local machine. Here is an example.

`docker-compose.yml`

```
services:
  activemq:
    image: dsi/activemq
    ports:
      - 8161:8161
      - 61616:61616

  mongodb:
    image: mongodb
    ports:
      - 27017:27017

  openstackswift:
    image: dsi/swiftserver
    ports:
      - "8888:8888"
```



Notice the container port binding with host, I can directly connect to any of these containers using the ports even my source code is outside of the docker container. This is amazing, all I need is one command `docker-compose up -d` to make my external dependencies ready in my development environment. We keep this `docker-compose.yml` file in our source code. When a new developer joins our team, s/he doesn't need to care about how to install an ActiveMQ server or a OpenStack Swift server. All s/he needs to do is, executing a single `docker-compose up` command and his/her development environment is ready.



### 2. Build docker container image

Once the development is done, it's time for building a container image out of it. Some people like to build the artifact, like jar or war inside docker while building the executable container image. But some people like build the artifact and then create the container image. Both approaches work just fine. To build a container image there are multiple ways to do it. You can write a `Dockerfile` and build the image using `docker build` command or you can use a third party tool.

There are some very popular tools to build container images, like

* [Jib](https://github.com/GoogleContainerTools/jib) (You don't need to write a `Dockerfile` or even install docker on your system if you want to do so)

* [fabric8io maven plugin](https://github.com/fabric8io/docker-maven-plugin) (You don't need to write a `Dockerfile` but docker needs to be installed in your system)

* [spotify maven plugin](https://github.com/spotify/docker-maven-plugin) (You need a `Dockerfile` and install `docker` in your host)

* [spring-boot-maven-plugin](https://spring.io/guides/gs/spring-boot-docker/) (If you're spring developer, spring has a plugin to build docker image)

  

All of them are great, but I like to keep the container image build instruction in a `Dockerfile`. There is another article of mine where I've discussed about the best practices to build a docker image. Check it out.



### 3. Push docker image

You've build your docker image, now what? You need to send the image to the server where you want to deploy the application or you want to share the image to a SQA engineer in your team. But how would you do it?

When you create a docker image you can't see any file gets created that you can copy to deployment server or someone else's computer. All you can do is see the image in your computer using  `docker image ls` or `docker images` command.

But you can extract the docker image to a .tar file which you can share with others. Here's how

**Step 1:** Save the docker image as a tar file

```
docker save -o <path/to/the/generated/file> <image name>
```

Example

```
docker save -o /tmp/myapp.tar myapp:1.0.0
```

**Step 2:** Copy the tar file to another computer and load the file using docker.

```
docker load -i <path/to/the/tar/file>
```


Example

```
docker load -i /tmp/myapp.tar
```

Now if you execute the command `docker images` you'll find the image listed in your remote machine.



But copying files manually isn't a convenient solution. If you have multiple QAs in your team or in production environment you might have multiple servers  then you've to copy it over to multiple computer. That's where registries come into picture and it's the most common way to share docker images. A registry is just an intermediary server where all the images are stored.  As a developer you will build the docker image and push the image in the registry using `docker push <image>` command. Those who wants to use the image they just need to pull the image using `docker pull <image>` command. [Docker hub](https://hub.docker.com/) is a very popular registry that you might already know. But if you're working on a private project and keep the image private and only in your system then, you can use the following alternatives:

**Self-hosted registry:**  [Harbor](https://goharbor.io/), [Artifactory](https://jfrog.com/artifactory/)

**Public cloud providers:** [Google Cloud’s Container Registry](https://cloud.google.com/container-registry/) [Amazon’s Elastic Container Registry (ECR)](https://aws.amazon.com/ecr/)



We use Harbor in our team and it's great so far. But you can choose any of the above,  all of them are heavily used in industries.



### 4. Deploy the container image to Server (Stage/Production Server)

The final step is to deploy the application so that end users can use it. You can use `systemd` to run the docker container or you can use `docker-compose` run the containers. Here is an example `systemd` script for a `tomcat` container running in Ubuntu.

`/etc/systemd/system/tomcat.service`

```
[Unit]
Description=Tomcat Container
After=docker.service
Requires=docker.service

[Service]
TimeoutStartSec=0
Restart=always
ExecStartPre=-/usr/bin/docker stop tomcat
ExecStartPre=-/usr/bin/docker rm tomcat
ExecStartPre=/usr/bin/docker pull tomcat
ExecStart=/usr/bin/docker run -p 8080:8080 --rm --name tomcat tomcat

[Install]
WantedBy=multi-user.target
```



But this is not a very convenient solution for production environment if you want 24/7 with zero downtime. If you want to update the application with a new version, there will be downtime in the above solution. Also you can't scale your application according to your need. So the recommended way is to use an orchestration tool like [Docker Swarm](https://docs.docker.com/engine/swarm/) or [Kubernetes](https://kubernetes.io/). 

But, these tools do come with some added complexity. If  24/7 zero downtime or on demand scale is not your primary concern then you can stick to the above solution. We did it for a certain time and many others are still  doing it happily.



### Using CI/CD Tools

Building artifact, building docker image, pushing it to the repository and deploy the container image in the production server can be an error prone and time consuming tasks. So many teams including us like to use a CI/CD tools like Travis CI, GitLab CI, Circle CI, Jenkins etc.

The developer just push the code in their version control system like Git and CI/CD tools pick up those changes and take care of the rest like test, build, push, deploy.

![workflow](https://raw.githubusercontent.com/afikur/afikur.github.io/master/assets/images/docker_for_java_dev.png)

### Summary

You're reading the summary that generally means you've read the whole article, you're awesome kudos to you. Docker is something that you can't learn just by reading some articles or watching few video tutorials, you've to make your hands dirty. Build something, containerize it, share your images with others, deploy it. You may find some challenges to do so, learn from your mistakes and keep learning.
