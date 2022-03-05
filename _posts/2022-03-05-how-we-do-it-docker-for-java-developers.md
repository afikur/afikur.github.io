---
layout: post
title: "What & Why Docker | Part I"
author: afikur
categories: [docker, java]
tags: [featured]
image: assets/images/docker_for_java_dev.png
---

#  How we do it: Docker for Java Developers


Before starting, I want to let you know that this article is not focused on very basics of containerization  technologies like [docker](https://www.docker.com/), [podman](https://podman.io/), [containerd](https://containerd.io/) etc. If you're an absolute beginner just take a course on introductory docker. There are tons of articles and video tutorials available online to learn the basics of docker.



But If you know basics of docker, you've installed all the necessary tools and now you're wondering 

> **What's the best process to follow and how people use docker in the real world?**

In this article I'll demonstrate how me and my team use docker in our day to day life to deploy mission critical production grade application.



To deploy a container image to production server there are basically four main stages.

1. Write code 
2. Build docker container image
3. Push docker image to docker registry (cloud/self-hosted)
4. Deploy the container to production server using a orchestration technology like Docker swarm or Kubernetes



### Write Code (and run on you local machine)

Some people like to keep their host machine clean and write code and develop using docker. That being said, your development machine won't contain anything like Java or Maven. You write the code and you keep a container running, all the changes you make that appears in the container. But, I find this hard and I moved on from this strategy. I like to keep my development environment as it is in my host machine. Once the development part is done then I containerize the code. So the important thing to remember -

> **You don't need do develop inside docker to deploy in docker**



Most of the time to develop your application, you need external dependency like a database or a object storage server installed on your machine. That's where you can really utilize the power of docker. Let's say you require a mongodb, activemq and a storage server to develop you code. You can run those external dependency in docker container. I use  [docker-compose](https://docs.docker.com/compose/gettingstarted/) in this situation  where multiple containers need to be run in my local machine. Here is an example.

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



Notice the port binding with host, I can directly connect to any of these container using the port even if my source code is outside of the docker container. This is amazing, all I need is one command `docker-compose up -d` to make my external dependencies ready in my development environment.



### Build docker container image

One the development is done, it's time for building a container image out of it. Some people like to build the artifact like jar or war inside docker while building the executable container image. But some people like build the artifact and then create the container image. Both approach works fine. 

To build a container image there are multiple ways to do it. You can write a `Dockerfile` and build the image using `docker build command` or you can use third party tools to do it.

There are some very popular tools to build container images

* [Jib](https://github.com/GoogleContainerTools/jib) (You don't need to write a `Dockerfile` or even install docker on your system if you want to do so)

* [fabric8io maven plugin](https://github.com/fabric8io/docker-maven-plugin) (You don't need to write a `Dockerfile` but docker needs to be installed in your system)

* [spotify maven plugin](https://github.com/spotify/docker-maven-plugin) (You need a `Dockerfile` and install `docker` in your host)

* [spring-boot-maven-plugin](https://spring.io/guides/gs/spring-boot-docker/) (If you're spring developer, spring has a plugin to build docker image)

  

All of them are great, but I like to keep the container image build instruction in a `Dockerfile`. There is another article of mine where I've discussed about the best practices to build a docker image. Check it out.



### Push docker image

You've build your docker image now what? You need to send the image to the server where you want to deploy the application or you want to share the image to a SQA engineer in your team. But how would you do it?

When you create a docker image you can't see any file gets created that you can copy to deployment server or someone else's computer. All you can do is see the image in your computer using  `docker image ls` or `docker images` command.

But you can extract the docker image to a file which you can share with others. Here's how



Step 1: Save the docker image as a tar file

```
docker save -o <path/to/the/generated/file> <image name>
```

Example

```
docker save -o /tmp/myapp.tar myapp:1.0.0
```



Step 2: Copy the tar file to another computer and load the file using docker.

```
docker load -i <path/to/the/tar/file>
```


Example

```
docker load -i /tmp/myapp.tar
```

Now if you execute command `docker images` you'll find the image listed in your remote machine.



But copying files manually isn't a convenient way. If you have multiple QAs in your team then you've to copy it over to multiple computer or in production environment you might have multiple servers as well. That's where registries come into picture and it's the most common way to share docker images. A registry is just an intermediary server where all the images are stored.  As a developer you will build the docker image and push the image in the registry using `docker push <image>` command. Those who wants to use the image they just need to pull the image using `docker pull <image>` command. [Docker hub](https://hub.docker.com/) is a very popular registry that you might already know. But if you're working on a private project and keep the image private and only in your system then, you can use the following alternatives:

**Self-hosted registry:**  [Harbor](https://goharbor.io/), [Artifactory](https://jfrog.com/artifactory/)

**Public cloud providers:** [Google Cloud’s Container Registry](https://cloud.google.com/container-registry/) [Amazon’s Elastic Container Registry (ECR)](https://aws.amazon.com/ecr/)



We use Harbor in our team and it's great so far. But you can choose any of the above,  all of them are heavily used in industries.





### Deploy the container image to Server (Stage/Production Server)

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

![workflow](assets/images/docker_for_java_dev.png)

### Summary

You're reading the summary that generally means you read the whole article, you're awesome kudos to you. Docker is something that you can't learn just by theory, you've to make your hands dirty. Build something, containerize it, share your images with others, deploy it. You may find some challenges to do so, learn from your mistakes and keep learning.
