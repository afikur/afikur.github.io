---
layout: post
title: "What & Why Docker | Part I"
author: afikur
categories: [docker]
image: assets/images/4.jpg
---

When I first heard about docker two questions came to my mind.

- What is Docker?
- Why Docker?

If I start explaining what is Docker, it probably won’t be understandable at first. So, just for now just imagine it’s a magic box. Bear with me till the end of this course I hope you’ll love this magic box.

But now let’s start by answering the question – why docker?

May be in the middle of the night you woke up seeing a dream of million dollar idea and decided to write the application right away. The application has some life cycle. First you have to build the application, then run it, then distribute it then you need to maintain or manage the application.

![img](https://firebasestorage.googleapis.com/v0/b/afikurcom.appspot.com/o/application-lifecycle-docker-lesson-afikur.com.jpg?alt=media&token=c560c14e-6d67-4beb-b91c-8809b4441d35?style=centerme)

You decided to write the application but you’ve some dependencies for that like language, dbs, servers, tools etc.

Let’s just imagine you’ll be needing these following technologies to build your application

- JDK (v1.8)
- Maven ( 3.6.2)
- Apache Tomcat (v8.5.47)
- Redis (v5.0.6)
- MySQL (v8.0.18)
- MongoDB 4.2.1

You started installing each of those in your system. But CRAP!! You might have faced something like this scenario.

![img](https://firebasestorage.googleapis.com/v0/b/afikurcom.appspot.com/o/application-installer-lifecycle-docker-lesson-afikur.com.jpg?alt=media&token=973d3622-ce05-4b8b-a01e-3db4a9560ebb)

Sometimes or should I say most of the times running an installer leads to an error. You troubleshoot it, then re run it and then get another error. Again troubleshoot it and re-run. And may be finally doing these steps a few times you successfully install the dependency in your system. But in the meantime, you’ve successfully wasted 8-10 hours just for creating a development environment in your computer. Phew.. Finally you started actual coding.

May be after developing some modules you decided to hire 3 more people for your project.

And guess what! Each of them started by doing installing the dependencies. And they need to setup the exact same version of your environment. Maybe they already had installed some of the dependencies with older version. It will conflict with the newer version. At the end each of them waste some amounts of times just for getting their environment ready. What a shameful way of wasting time!!

![img](https://firebasestorage.googleapis.com/v0/b/afikurcom.appspot.com/o/each-use-needs-to-setup-exact-environment-docker-lesson-afikur.com.jpg?alt=media&token=4b720a9b-46aa-48c1-90f2-8b564f6411d8)

Each of the team members need exact same version of every tools and technologies

If you can relate this with yourself, don’t be sad, you’re not alone. Most of the developer has faced these issues in their lifetime.

If I say with the help of docker you’ll never face something like this ever! Yes you heard me right. You just need docker installed in you machine and then docker guarantees you will never see something like this ever in your life.

You just say something like this

```bash
docker run mongo
```

But is this the only reason why you’ll use docker? Probably not. It’s something you get from docker out of the box, it’s really helpful for a developer but there are other tools as well for doing just this. There are much greater reason for using docker. And we’ll talk about that in the next lesson.
