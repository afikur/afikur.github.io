---
layout: post
title: "How to install jdk8 on ubuntu 18.04"
author: afikur
categories: [java]
image:
---

# Step 1: Download Oracle Java 8

https://www.oracle.com/technetwork/java/javase/downloads/java-archive-javase8-2177648.html

# Step 2: Copy downloaded Setup and Extract

Create a directory:

```
sudo mkdir  -p /opt/jdk
```

Copy the tar file from the directory copied from local machine to server to /opt/jdk folder and execute below command:

```
sudo cp -rf /home/afikur/jdk-8u202-linux-x64.tar.gz /opt/jdk/
```

```
cd /opt/jdk/
```

```
sudo tar -zxf jdk-8u202-linux-x64.tar.gz
```

Now unarchived the file and check the content by long-list:

```
ls
```

# Step 3: Install Oracle Java 8 on ubuntu with Alternatives

Use update-alternatives command to configure java on your system

```
sudo update-alternatives --install /usr/bin/java java /opt/jdk/jdk1.8.0_202/bin/java 100
```

After executing above command it shows below output:

Output:

```
update-alternatives: using /opt/jdk/jdk1.8.0_202/bin/java to provide /usr/bin/java (java) in auto mode
```

# Step 4: Verify Update Alternatives

```
sudo update-alternatives --display java
```

Output:

```
java - auto mode
link best version is /opt/jdk/jdk1.8.0_202/bin/java
link currently points to /opt/jdk/jdk1.8.0_202/bin/java
link java is /usr/bin/java
/opt/jdk/jdk1.8.0_202/bin/java - priority 100
/opt/jdk/jdk1.8.0_202/bin/javac - priority 100
```

To change for alternative mode:

```
sudo update-alternatives --config java
```

It prompts for selecting by 0,1,2..so choose accordingly:
Output

There are 2 choices for the alternative java (providing /usr/bin/java).

## Selection Path Priority Status

- 0 /opt/jdk/jdk1.8.0_202/bin/java 100 auto mode

  1 /opt/jdk/jdk1.8.0_231/bin/java 100 manual mode

  2 /opt/jdk/jdk1.8.0_202/bin/javac 100 manual mode

Press <enter> to keep the current choice[*], or type selection number:

# Step 5: Setting the JAVA_HOME and JRE_HOME Environment Variables

To define the enviroment variable:

```
sudo  nano /etc/environment
```

Paste the below varible on the file:

```
JAVA_HOME=/opt/jdk/jdk1.8.0_202
JRE_HOME=/opt/jdk/jdk1.8.0_202/jre
```

To check variables defined:

```
source /etc/environment
```

```
echo $JAVA_HOME
```

Output:

```
/opt/jdk/jdk1.8.0_202
```

```
sudo apt-get update
```

# Step 6: Verify Java Version

To check the java version:

```
java -version
```

Output:

```
java version "1.8.0_202"
Java(TM) SE Runtime Environment (build 1.8.0_202-b08)
Java HotSpot(TM) 64-Bit Server VM (build 25.202-b08, mixed mode)
```

If you seeing output like above then we have successfully set up the Java 8 on Ubuntu.

# Conclusion

In this article, We have downloaded Oracle Java 8 from official site, installed using command line, configured JAVA_HOME and JRE_HOME, verified installed version.
