---
layout: post
title: "Spring Boot Application as a Service on Ubuntu Server"
author: afikur
categories: [spring]
tags: [featured]
image: assets/images/spring-boot-as-a-service-on-ubuntu.jpg
---

Building Your Spring Boot Application
Run the following command inside your application's root directory:

```sh
mvn clean package
```

The executable JAR file is now available in the target directory. By executing the following command on the command line we can run the application

```sh
java -jar my-spring-boot-app-0.0.1-SNAPSHOT.jar
```

But the application is now running on foreground. So you might want to run it in detached mode or as a background process. So you might add an `&` at the end of the earlier command. In this way process goes into background and your terminal is free. But is it a good way of running your application?

The answer is No. You might want to see your spring standard output logs or store it somewhere. Also you may need to restart the application. Or you might need to restart the server and when the server is up again you want your application to startup automatically.

For this in mind, you might need to write a systemd service on linux. What is a systemd service?

_A unit configuration file whose name ends in "`.service`" encodes information about a process controlled and supervised by systemd._

With the power of systemd service gives you much more control over your application startup.

Before creating the service let's copy our application to this directory `/usr/local/applications/my-spring-boot-app/`. This could be any directory. It's totally your choice. But make sure the user who runs the application has the proper permissions. For Linux group and user permissions in mind. Let's create a user and a group just for deploying the application. I'll be creating a user and a group with the same name `spring`. It can be any name you want.

```
sudo mkdir /usr/local/applications
sudo useradd -U -d /usr/local/applications -M -s /bin/false spring
sudo chown -R spring:spring /usr/local/applications
```

Our `.jar` is placed where we wanted. Let's now write a systemd service for our spring boot application.

Run the following command to create a service named `my-spring-boot-app.service`. All the service ends with a `.service` extension

```sh
sudo nano /etc/systemd/system/my-spring-boot-app.service
```

Now put the following contents into the file

```sh
[Unit]
Description=A Spring Boot application
After=syslog.target

[Service]
User=spring
Group=spring

ExecStart=/usr/bin/java -jar /usr/local/applications/my-spring-boot-app-0.0.1-SNAPSHOT.jar
SuccessExitStatus=143

[Install]
WantedBy=multi-user.target
```

Next, reload the systemd daemon using the following command:

```
sudo systemctl daemon-reload
```

Start the application using the following command:

```
sudo systemctl start my-spring-boot-app.service
```

To check the status of the application run the following command:

```
sudo systemctl status my-spring-boot-app.service
```

If you want to see the logs output of the service, you can you journalctl

```
sudo journalctl -u my-spring-boot-app.service -f
```

`-u` , `--unit` is to specify the systemd unit

`-f`, `--follow` to show the most recent entries
