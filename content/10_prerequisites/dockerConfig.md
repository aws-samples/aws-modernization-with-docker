---
title: "Configure Docker on Cloud9"
chapter: false
weight: 21
---


We need to remove docker and install a different version of Docker.

1. Open the terminal on Cloud9 and run the following: `sudo yum -y remove docker`. 
2. run `sudo yum install -y yum-utils`
3. run `sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo`
2. Now run the following in install Docker Engine: `sudo yum install docker-ce docker-ce-cli containerd.io`