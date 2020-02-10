---
layout: post
title: "How to install Apache Maven on Ubuntu 18.04"
author: afikur
categories: [maven]
image: assets/images/maven-on-ubuntu.jpg
---

# Installing Apache Maven on Ubuntu with Apt

Installing Maven on Ubuntu using `apt` is a simple, straightforward process.

### Step 1: update package index

```
sudo apt update
```

### Step 2: Install Maven

```
sudo apt install maven
```

### Step 3: Verify installation by checking maven version

```
mvn -version
```

If the installation is successful, you can see something like this below

```
Apache Maven 3.6.0
Maven home: /usr/share/maven
Java version: 1.8.0_201, vendor: Oracle Corporation, runtime: /usr/lib/jvm/java-8-oracle/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "5.3.0-28-generic", arch: "amd64", family: "unix"
```

# Install the Latest Release of Apache Maven

### Step 1: Install JDK

We're going to install OpenJDK but if you want to install Oracle JDK, Checkout my other article where I've shown step by step to install OracleJDk 1.8

https://afikur.com/how-to-install-jdk8-on-ubuntu-18.04/

```
sudo apt update

sudo apt install default-jdk

java -version
```

If the installation is successful, you can the java version

### Step 2: Download Apache Maven

Go to this URL to download the latest versoin of apache maven: http://maven.apache.org/download.cgi

Currently the latest versoin is 3.6.3. I'm using `wget` to download apache maven

```
cd /usr/local

sudo wget https://www-us.apache.org/dist/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz

sudo tar xzvf apache-maven-3.6.3-bin.tar.gz

sudo rm apache-maven-3.6.3-bin.tar.gz
```

We downloaded and extracted the tar.gz file in `/usr/local` directory.

To have more control over Maven versions and updates, we will create a symbolic link maven that will point to the Maven installation directory

```
sudo ln -s /usr/local/apache-maven-3.6.3 /opt/maven
```

Later if you want to upgrade your Maven installation you can simply unpack the newer version and change the symlink to point to the latest version.

### Step 3: Setup environment variables

We’ll need to set up the environment variables. To do so, open your text editor and create a new file named `maven.sh` inside of the `/etc/profile.d/` directory

```
sudo nano /etc/profile.d/maven.sh
```

Paste the following configuration

```
export JAVA_HOME=/usr/lib/jvm/default-java
export M2_HOME=/opt/maven
export MAVEN_HOME=/opt/maven
export PATH=${M2_HOME}/bin:${PATH}
```

You might need to change the `JAVA_HOME` path to your machine's java home. Make sure to point the JAVA_HOME correctly.

Make the `maven.sh` file executable by running the following command

```
sudo chmod +x /etc/profile.d/maven.sh
```

Finaly, load the environment variables using the source command:

```
source /etc/profile.d/maven.sh
```

### Step 4: Verify installation by checking maven version

```
mvn -version
```

If the installation is successful, you can see something like this below

```
Apache Maven 3.6.3
Maven home: /opt/maven
Java version: 1.8.0_242, vendor: Private Build, runtime: /usr/lib/jvm/java-8-openjdk-amd64/jre
Default locale: en, platform encoding: UTF-8
OS name: "linux", version: "4.15.0-76-generic", arch: "amd64", family: "unix"
```

If you encounter any problem or have feedback, leave a comment below. Thanks
