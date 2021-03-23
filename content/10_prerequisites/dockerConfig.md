---
title: "Configure Docker on Cloud9"
chapter: false
weight: 21
---


Your Cloud9 environment comes prebuilt with a version of Docker that doesn't have the features that we will need for this workshop. Please follow the steps below to install and verify that you have the correct version

1. Open the terminal on Cloud9 and run the following: 
```
curl -L -o docker-linux-amd64.tar.gz https://github.com/docker/compose-cli/releases/download/v1.0.10/docker-linux-amd64.tar.gzz
tar xzf docker-linux-amd64.tar.gz
chmod +x docker/docker
``` 
2.  To enable using the local Docker Engine and to use existing Docker contexts, you will need to have the existing Docker CLI as com.docker.cli somewhere in your PATH. You can do this by creating a symbolic link from the existing Docker CLI.

```
$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
$ which docker
/usr/bin/docker
$ sudo ln -s /usr/bin/docker /usr/local/bin/com.docker.cli
```

3. You can verify that this is working by checking that the new CLI works with the default context:
```
$ ./docker/docker --context default ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
$ echo $?
0
```

4. To make the Compose CLI your default Docker CLI, you must move it to a directory in your PATH with higher priority than the existing Docker CLI.

```
$ which docker
/usr/bin/docker
$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
$ sudo mv docker/docker /usr/local/bin/docker
$ which docker
/usr/local/bin/docker
$ docker version
...
 Client: Docker Engine - Community
 Version:           20.10.5
 API version:       1.41
 Go version:        go1.13.15
 Git commit:        55c4c88
 Built:             Tue Mar  2 20:18:05 2021
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.5
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.13.15
  Git commit:       363e9a8
  Built:            Tue Mar  2 20:16:00 2021
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.4.4
  GitCommit:        05f951a3781f4f2c1911b05e61c160e9c30eaa8e
 runc:
  Version:          1.0.0-rc93
  GitCommit:        12644e614e25b05da6fd08a38ffa0cfe1903fdec
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
...
```
You have now completed all the steps necessary to go through the workshop on your Cloud9 workspace. 