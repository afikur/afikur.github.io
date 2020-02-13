---
layout: post
title: "How to install Apache Tomcat 8 on Ubuntu 18.04"
author: afikur
categories: [tomcat]
image: assets/images/tomcat-on-ubuntu.png
---

Follow these steps to download and then install Apache Tomcat server on your Ubuntu
machine

### Step 1: Download Apache Tomcat

Move to the /tmp directory and download the Tomcat application using the wget command, as shown here:

Go to this URL https://tomcat.apache.org/download-80.cgi for latest tomcat 8 version.

```sh
cd /tmp
wget https://www-eu.apache.org/dist/tomcat/tomcat-8/v8.5.51/bin/apache-tomcat-8.5.51.tar.gz
```

### Step 2: Extract downloaded tomcat

Create a new directory `/opt/tomcat` using the following command.

```sh
sudo mkdir /opt/tomcat
```

Untar the content of the archive inside /opt/tomcat

```sh
sudo tar xzvf apache-tomcat-8*tar.gz -C /opt/tomcat --strip-components=1
```

### Step 3: Create a systemd service

Crate a file using the following command.

```sh
sudo nano /etc/systemd/system/tomcat.service
```

Paste the following content into the file and save the file. Type `Ctrl + X` and choose `Y` to save and close.

```bash
[Unit]
Description=Apache Tomcat Web Application Container
After=network.target

[Service]
Type=forking

Environment=JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-amd64
Environment=CATALINA_PID=/opt/tomcat/temp/tomcat.pid
Environment=CATALINA_HOME=/opt/tomcat
Environment=CATALINA_BASE=/opt/tomcat
Environment='CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC'
Environment='JAVA_OPTS=-Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom'

ExecStart=/opt/tomcat/bin/startup.sh
ExecStop=/opt/tomcat/bin/shutdown.sh

RestartSec=10
Restart=always

[Install]
WantedBy=multi-user.target
```

Next, reload the systemd daemon using the following command:

```sh
sudo systemctl daemon-reload
```

Start the Tomcat service using the following command:

```sh
sudo systemctl start tomcat
```

To check the status of Tomcat service, run the following command:

```sh
sudo systemctl status tomcat
```

You should see something like the following output:

```sh
● tomcat.service - Apache Tomcat Web Application Container
   Loaded: loaded (/etc/systemd/system/tomcat.service; disabled; vendor preset: enabled)
   Active: active (running) since Thu 2020-02-13 11:28:46 UTC; 7s ago
  Process: 2240 ExecStart=/opt/tomcat/bin/startup.sh (code=exited, status=0/SUCCESS)
 Main PID: 2257 (java)
    Tasks: 30 (limit: 2318)
   CGroup: /system.slice/tomcat.service
           └─2257 /usr/lib/jvm/java-1.8.0-openjdk-amd64/bin/java -Djava.util.logging.config.file=/opt/tomcat/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djava.awt.headless=true -Djava.security

Feb 13 11:28:46 ubuntu-server-1 systemd[1]: Starting Apache Tomcat Web Application Container...
Feb 13 11:28:46 ubuntu-server-1 startup.sh[2240]: Tomcat started.
Feb 13 11:28:46 ubuntu-server-1 systemd[1]: Started Apache Tomcat Web Application Container.
```

### Step 4: Enabling the firewall and port 8080

Enable the firewall using the following command:

```sh
sudo ufw enable
```

Allow traffic on port 8080 :

```sh
sudo ufw allow 8080
```

Enable OpenSSH to allow SSH connections using the following command:

```sh
sudo ufw enable "OpenSSH"
```

Check the firewall status using the following command:

```sh
sudo ufw status
```

You should see the following output:

```sh
Status: active

To                         Action      From
--                         ------      ----
8080                       ALLOW       Anywhere
8080 (v6)                  ALLOW       Anywhere (v6)
```

You should now be able to access the Apache Tomcat server page at http://<IPaddress of the Apache Tomcat>:8080
