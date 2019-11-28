---
layout: post
title: "What & Why Docker | Part II"
author: afikur
categories: [docker]
image: assets/images/3.jpg
---

Before starting answering the question why docker, let’s go back to old days when we didn’t have docker.

#### Flashback 1990s

We all know that we need a server (or your laptop that has a server installed) to run an application. Let’s say you’ve four different application. So you buy four server computer to run the four application. If you’re new then you might ask why do we need four?

Let’s answer this question first. It’s a good practice to run one application in one isolated server. why is that? let’s see this scenario –

There are four applications, all installed on the same server where the network shares reside. There are times when the server needs to be restarted because of a particular application issue, this mean all the other applications are not accessible in that time. But if you had different server for each application. If application one needs to be restarted then application two/three/four is running without any issues.

There’re many good reasons to keep application separated, but you got the idea right?

So, back old days for each of the application, there was a server. A server like below picture, a big fat expensive rack server!

![img](https://firebasestorage.googleapis.com/v0/b/afikurcom.appspot.com/o/rack-server.png?alt=media&token=06450f09-9a0a-428d-b391-5ec04e52f47d&style=centerme)A physical rack server

So, as I said, suppose your company owns 4 different applications, each of them use separate servers like below picture

![img](https://firebasestorage.googleapis.com/v0/b/afikurcom.appspot.com/o/each-app-each-virtual-machine-architecutre-docker-lesson-afikur.com.jpg?alt=media&token=446b4221-809c-44b4-8174-cfcd35afd85e&style=centerme)Each app is running on it’s own server

Few days later your company decided to launch another application for whatever reason. So they need to buy another server. But what type of server? I mean, how much core of CPU? how much RAM? how much storage? And the question goes on

But unfortunately there’s no right answer for those questions. NOBODY actually knows the answer. So, for safety reason they buy a big fat expensive high performant server. But after running the application for months and collecting all the server usage statistics you saw that it usage only 5-10% resource of that server. holy moly! we successfully wasted company resource and capital!

![img](https://firebasestorage.googleapis.com/v0/b/afikurcom.appspot.com/o/one-app-one-vm-back-old-days-virtual-machine-architecutre-docker-lesson-afikur.com.jpg?alt=media&token=9d055bde-3903-49d6-bd84-071ae4d677bf&style=centerme)

This was a real problem until VMWare came up with a great solution. Era of VMWare!

#### After 1998

VMware solved this problem, where we used to put one application per server. Now with the help of hypervisor technology we can run each app per VM. Let’s have a look of below picture

![virtual machine](https://firebasestorage.googleapis.com/v0/b/afikurcom.appspot.com/o/virtual-machine-architecutre-docker-lesson-afikur.com.jpg?alt=media&token=61baaffe-cef7-4857-8b14-e471727bb6ba&style=centerme)Virtual Machine

If I explain this diagram shortly, then at the bottom gray colored box is our physical server. On top of it, there’s a hypervisor. And on top of it, there’s 4 VMs. Each VM share 25% of CPU, RAM, Disk Space, NIC (Network Interface Card). You need to really understand this isolation. In each VM, it slice and dice the physical server and create a Virtual CPU, Virtual RAM, Virtual Disk Space, virtual NIC etc. In Each VM we install an Operating System. But the operating system has no idea that the CPU, RAM it’s using is actually virtual. It acts like a normal physical server. The OS Kernel use this virtual disk space to write, but under the hood the hypervisor technology writes it in the actual disk.

So, Each VM has it’s own OS. It is totally isolated from other VMs. So in each OS we run our application.

One single physical server now can run four different application with isolation in mind. If VM01 needs to restart, other VMs doesn’t get affected, Also, now we can use the server performance most out if it, like 70-80%. HALLELUJAH!!

It seems like, VMWare solves our problem perfectly. But does it? Let’s see

In Each VM, there’s an OS. For running an operating system, it needs resources as well. It consumes some amount of CPU, RAM, disk space from each virtual machine

For each of the OS you need to buy licence from the provider.

Also, think about it, you need an admin to manage all the Operating Systems for updating, patching, driver support etc.

![virtual machine drawbacks](https://firebasestorage.googleapis.com/v0/b/afikurcom.appspot.com/o/virtual-machine-architecutre-drawbacks-docker-lesson-afikur.com.jpg?alt=media&token=944d0433-8d80-4d01-b170-0cb3713fa535&style=centerme)VM Drawbacks

Even though MVWare and hypervisor technology is very powerful and rich but it had few drawbacks which doesn’t feel legit, right?

This is where docker comes into place and solves the problem. Lets’s come back from flashback and talk about current Docker world.

#### Docker

Unlike doing hardware-level virtualization, Docker actually does OS-level virtualization. We install one Operating System in our physical server. In there we install docker. Then we can run multiple containers in a single OS. The containers are isolated from each other like MV, but in a different way

![img](https://firebasestorage.googleapis.com/v0/b/afikurcom.appspot.com/o/docker-architecutre-docker-lesson-afikur.com.jpg?alt=media&token=14986a73-6b09-4299-8c49-4546d498c820&style=centerme)

Each docker container has virtual namespace and control groups of the Operating system. So if you see first container it has separate process id (pid), network(net), filesystem (mnt) etc. from other containers. container is a standard unit of software that packages up code and all its dependencies. For example, lets say in first container we want to run a Spring boot application. In that case in first container there would be java installed. In the second container may be you’re running NodeJS application. So there would NodeJS, but you won’t find Java there.

> VM is hardware-level virtualization where Docker is OS-level virtualization.

Let’s compare virtual machine and docker side by side.

![virtual box vs docker](https://firebasestorage.googleapis.com/v0/b/afikurcom.appspot.com/o/virtual-machine-architecutre-vs-docker-architecture-docker-lesson-afikur.com.jpg?alt=media&token=dbb7cdd1-e188-4aae-a7de-040c9ca67865&style=centerme)

So with the help of docker you can run your application in an isolated environment but here you don’t need separate OS for each application. You’ve an isolated environment where it has only your source code with the dependencies you need to run the code. And we’re calling it docker containers. Docker containers are lightweight and way faster than VM.

> A container is a standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another. A Docker container image is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries and settings.
>
> docker.com

This concludes our answer, why do we need docker. I guess you’ve somehow figured out what is docker as well. Let’s try to understand the definition of docker from Wikipedia. If you’ve read so far, trust me you’ll understand the definition.

> Docker is a set of platform-as-a-service (PaaS) products that use OS-level virtualization to deliver software in packages called containers. Containers are isolated from one another and bundle their own software, libraries and configuration files. they can communicate with each other through well-defined channels. All containers are run by a single operating-system kernel and are thus more lightweight than virtual machines.
>
> wikipedia

I hope, now you know what is docker and why do we need it. In the next lesson, we’ll install docker in our machine and start using docker.