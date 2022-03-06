---
layout: post
title: "Best practices to build a Java container with Docker"
author: afikur
categories: [docker, java]
tags: [featured]
image: assets/images/docker1.jpg
---

# Best practices to build a Java container with Docker



### Use JRE not JDK



When creating a docker image for running a java application, we need a java runtime environment. There's no need to use a full blown JDK just to run the app.



```
FROM openjdk:11-jdk
EXPOSE 8080
ARG JAR_FILE=target/demo-0.0.1-SNAPSHOT.jar
ADD ${JAR_FILE} app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
```



It's absolutely unnecessary to use JDK here. We could just use a JRE which will reduce the docker image size. For farther reducing the docker  we can use the slim version*, like `openjdk:11-jre-slim` as a base image. So just change the first line with the following.



```
FROM openjdk:11-jre-slim
```



\* -jre is the runtime environment for java.

\*  - slim is a paired down version of the full image. This image only installs the minimal packages needed to run a particular tool like java.



**Size comparison**

| Image Name          | Image Size |
| ------------------- | ---------- |
| openjdk:11-jdk      | 660 MB     |
| openjdk:11-jre      | 307 MB     |
| openjdk:11-jre-slim | 228 MB     |



We can further improve this docker image and we'll discuss about that through out this article.





### Never run container as root



When you create a container, by default it's root. But root can do anything inside the container which is a security risk. But we can solve this by creating a custom user and group in our docker container and run the container with that non-root user.



```
FROM openjdk:11-jre-slim
EXPOSE 8080
ARG JAR_FILE=target/demo-0.0.1-SNAPSHOT.jar
RUN mkdir /app \
    && groupadd --system --gid 3000 spring \
    && useradd --system --uid 1000 --shell /bin/false --gid spring spring
ADD ${JAR_FILE} /app/app.jar
RUN chown -R spring:spring /app
USER spring
WORKDIR /app
ENTRYPOINT ["java","-jar","app.jar"]
```



Here, we've done few steps -

* Created a directory named `/app`
* Added a system group with id 3000 and a system user with id 1000
* Changed the directory owner to the newly created user.
* Then by `USER spring`  command, we've set the user to run the container.



**Note**: Kubernetes security context requires the group and user Id. That' s why we chose to create a deterministic id for both user and group.





### Minimize the number of layers



You need to find the balance between readability (and thus long-term maintainability) of the Dockerfile and minimizing the number of layers it uses. Be strategic and cautious about the number of layers you use. 

Only the instructions RUN, COPY, ADD create layers. Other instructions create temporary intermediate images, and do not increase the size of the build.



You may want to use the `RUN` command for reducing the layers but there are scenarios where you absolutely should use single run command and execute multiple Linux command. Here is why - 



Dockerfile:

```
FROM ubuntu:18.04
RUN apt-get update
RUN apt-get install -y curl
```

After building the image, all layers are in the Docker cache. Suppose you later modify `apt-get install` by adding extra package `nginx` in it:

```
FROM ubuntu:18.04
RUN apt-get update
RUN apt-get install -y curl nginx
```

Docker sees the initial and modified instructions as identical and reuses the cache from previous steps. As a result the `apt-get update` is *not* executed because the build uses the cached version. Because the `apt-get update` is not run, your build can potentially get an outdated version of the `curl` and `nginx` packages.

So the best way to do it is

```
FROM ubuntu:18.04
RUN apt-get update && apt-get install -y \
    curl \
    nginx
    && rm -rf /var/lib/apt/lists/*
```





### Use multi-stage builds



In Docker for Java Developer article, I told you that we do not need to build our Java application in a container. We only need the artifact (jar/war) to build the docker image. However, in some cases, it is convenient to build application inside docker image.

Luckily we can create a Docker image into multiple stages, where we can separate the build and production stage. We can create a build image using all the tools we need to build our application and create a second stage where we create the actual production image. So, in the final image the container image won't contain any unnecessary tools that doesn't require to run the application, thus the image size is much much smaller. Here is an example for you



```
# build the artifcat
FROM maven:3.6.3-jdk-11-slim as build
RUN mkdir /project
COPY . /project
WORKDIR /project
RUN mvn clean package
  
# final production ready image
FROM openjdk:11-jre-slim
EXPOSE 8080
RUN mkdir /app \
    && groupadd --system --gid 3000 spring \
    && useradd --system --uid 1000 --shell /bin/false --gid spring spring
COPY --from=build /project/target/application.jar /app/application.jar
RUN chown -R spring:spring /app
USER spring
WORKDIR /app
ENTRYPOINT ["java","-jar","app.jar"]
```





### Use Spring Boot Layer Index

If you're using spring boot 2.3 or higher and if you extract the the jar file, you'll see there are some layers created for you. You can take advantages of that. We extracted each of the layers like dependencies, spring-boot-loader, snapshop-dependencies and application.

But what's the advantage of doing that you might ask. You already know about caching mechanism of building docker image. If any layer doesn't get change docker doesn't run that layer again, instead it start with the last layer that has been changed or updated. When we develop an application we don't frequently update our dependencies, rather we update our source code. So, if we separate the dependencies layer then the subsequent docker build for that application will be faster as well as it will produce smaller docker image. To know details you can check [this link]( https://spring.io/guides/topicals/spring-boot-docker/).



Here is an example:

```
FROM openjdk:11-jre-slim as build
ARG JAR_FILE=target/demo-0.0.1-SNAPSHOT.jar
ADD ${JAR_FILE} app.jar
RUN mkdir -p target/extracted \
    && java -Djarmode=layertools -jar app.jar extract --destination target/extracted

FROM openjdk:11-jre-slim

ARG EXTRACTED=/target/extracted

RUN mkdir /app \
    && groupadd --system --gid 3000 spring \
    && useradd --system --uid 1000 --shell /bin/false --gid spring spring

COPY --from=build ${EXTRACTED}/dependencies/ /app
COPY --from=build ${EXTRACTED}/spring-boot-loader/ /app
COPY --from=build ${EXTRACTED}/snapshot-dependencies/ /app
COPY --from=build ${EXTRACTED}/application/ /app

RUN chown -R spring:spring /app
USER spring
WORKDIR /app
EXPOSE 8080
ENTRYPOINT ["java","org.springframework.boot.loader.JarLauncher"]
```



### Use container-aware Java



Your best choice is to update to a newer version of Java, beyond 10 in order to have container support activated by default. Unfortunately, many companies are still relying heavily on Java 8. This means you should either update to a more recent version of Java in your Docker images or make sure that use at least Java 8 update 191, or higher. 



### Use .dockerignore



To exclude files not relevant to the build (without restructuring your source repository) use a `.dockerignore` file. This file supports exclusion patterns similar to `.gitignore` files. An example .dockerignore file is

```
**/*.log
Dockerfile
.git
.gitignore
.env
```



### Summary

I've mentioned few best practices of building docker image. But there are more than this, that you should follow. I've tried to give all the example in java so that Java developers can connect to it easily. Please check the official best practices guide by Docker. [https://docs.docker.com/develop/develop-images/dockerfile_best-practices/]( https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

