---
layout: post
title: "Installing Docker in your Machine"
author: afikur
categories: [docker]
image: assets/images/docker3.jpg
---Docker comes in two flavors.

- The Community Edition (CE)
- The Enterprise Edition (EE)

Now you might be asking what’s the differences.

- Docker CE (Community Edition) is the simple classical OSS (Open Source Software) Docker Engine.
- Docker EE (Enterprise Edition) is Docker CE with certification on some systems and support by Docker Inc. If you want to know more about Enterprise Edition visit [this link](https://www.docker.com/blog/docker-enterprise-edition/) and [this link](https://www.docker.com/blog/docker-online-meetup-recap-docker-enterprise-edition-ee-community-edition-ce/).
- Docker CS (Commercially Supported) is kind of the old bundle version of Docker EE for versions <= 1.13

In this course we’ll be using docker CE. You can run docker CE in Linux, Mac or Windows. Let’s start using Docker.

#### Supported platforms

Docker Engine – Community is available on multiple platforms. Use the following tables to choose the best docker installation path for you. You might need to create an account in docker hub for downloading the binary executor for windows and mac. But it’s quite easy and in the links you’ll get all the instruction you may need to install docker.

#### Desktop

| Platform                                                                                                 | x86_64 |
| :------------------------------------------------------------------------------------------------------- | :----: |
| [Docker Desktop for Mac (macOS)](https://docs.docker.com/docker-for-mac/install/)                        |   🗹    |
| [Docker Desktop for Windows (Microsoft Windows 10)](https://docs.docker.com/docker-for-windows/install/) |   🗹    |

#### Server

| Platform                                                          | x86_64 / amd64 | ARM | ARM64 / AARCH64 | IBM Power (ppc64le) | IBM Z (s390x) |
| :---------------------------------------------------------------- | :------------: | :-: | :-------------: | :-----------------: | :-----------: |
| [CentOS](https://docs.docker.com/install/linux/docker-ce/centos/) |       🗹        |     |        🗹        |                     |               |
| [Debian](https://docs.docker.com/install/linux/docker-ce/debian/) |       🗹        |  🗹  |        🗹        |                     |               |
| [Fedora](https://docs.docker.com/install/linux/docker-ce/fedora/) |       🗹        |     |        🗹        |                     |               |
| [Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/) |       🗹        |  🗹  |        🗹        |          🗹          |       🗹       |

After completing installing docker. Run the following command in your terminal/command prompt/powershell

```bash
docker --version
```

If you can see the version number, congrats! you successfully installed docker in your machine.

In the next article, we’ll create our first docker container
