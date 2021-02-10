---
title: "Configure Docker on Cloud9"
chapter: false
weight: 21
---


Your Cloud9 environment comes prebuilt with a version of Docker that doesn't have the features that we will need for this workshop. Please follow the steps below to install and verify that you have the correct version

1. Open the terminal on Cloud9 and run the following: `curl -L https://raw.githubusercontent.com/docker/compose-cli/main/scripts/install/install_linux.sh | sh`. 
2. Verify that the docker compose cli has been install correctly: `docker compose --help` If the tool has been installed correctly you should see the following

![docker](/images/docker compose screenshot 1.png)
