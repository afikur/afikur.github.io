---
layout: post
title: "Docker for Java Developers | Running a simple Java App"
author: afikur
categories: [docker]
image: assets/images/7.jpg

---

So far, we’ve seen hello-world and tomcat images. hello-world is nothing but a C program that only outputs a greeting text. Tomcat is on the other hand an useful image. But we didn’t deploy any of our app in that tomcat container. But don’t worry we’ll do that in near future. But before that let’s explore few other docker images and learn few key features that docker provides and we might be needing in near future.

### Working With Java

Let’s compile and run a Java program but using docker container. No need to install JDK on your host machine anymore. Docker will handle that for you. Let’s get started.

Let’s write an application in Java which just show the current date in the console. Nothing fancy for now. Create `App.java` file. Directory structure is

```bash
my-app
  src
    App.java
```

Inside our project root folder. There’s a src folder. Inside the src dir our application code (App.java) is placed.

```java
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

public class App {
    public void display() {
        DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy/MM/dd hh:mm:ss a");
        LocalDateTime now = LocalDateTime.now();
        System.out.println("****************************************");
        System.out.println("* Current Time: " + dtf.format(now) + " *");
        System.out.println("****************************************");
    }

    public static void main(String[] args) {
        new App().display();
    }
}
```

### Compiling with javac

Now, we need to compile the code in order to run it. So let’s give docker the responsibility to do that

Go to your project root directory in the terminal then run this command

```docker
docker run -v $PWD/src:/src -w /src openjdk:8 javac App.java
```

There are two new more flag we’ve introduced in this command. -v and -w. Let’s talk about that a little bit

| –volume , -v  | Bind mount a volume                    |
| ------------- | -------------------------------------- |
| –workdir , -w | Working directory inside the container |

By -v or –volume flag we’re mounting our host machine folder with the docker container folder. `$PWD` means the current folder location then we’re appending `/src`. Cause the `src` folder we want to mount with container.

[HOST_FOLDER]**:**[CONTAINER_FOLDER]

Just like –publish or -p flag the first part of the colon is our host, after colon the container. So anything changes to our host or the container, we’ll get changes on both host and container, cause it’s mounted. It’s the purpose of mounting a folder, we get changes both sides, may be you’re already familiar with mounting, but if not, you’re now familiar with mounting.

Another flag is `-w` or `--workdir` which represent the working directory inside the container. So we told docker that `/src` folder in our container is the working directory. So when we trying to compile our App.java, it’s in the root of our workdir. So we just can write this following in our container. It’s a java image so, we’re expecting javac will be there inside the container. Make sense?

```bash
javac App.java
```

It’s going to compile and create another file called `App.class`. As the `src` folder is mounted with docker container, we’ll get the `App.class` in our host as well.

### Running the app

Now try to run the App.class but with the help of docker container. It’s fairly straight forward to run. If you can compile the code using docker you can run the code as well. But for whatever reason, if you fail to do that, don’t loose hope, at the end of this lesson I’ll give you the solution. You can compare your solution with mine.

### Docker run

`docker run` command is one of the most used command in docker. We’ve used this command already with some optional flags as well. But let’s talk about some commonly used options for `docker run` command in the next article.

### Solution

```docker
docker run -v $PWD/src:/src -w /src openjdk:8 java App
```