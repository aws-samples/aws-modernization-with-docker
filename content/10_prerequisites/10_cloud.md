---
title: "Update IAM settings for your Workspace"
chapter: false
weight: 19
---

## Configure workspace for Docker Workshop

{{% notice info %}}
Cloud9 normally manages IAM credentials dynamically. However, for the purpose of this workshop, we need the temporary tokens to be in environment variables.
{{% /notice %}}

1. Return to your workspace and click the gear icon (in top right corner), or click to open a new tab and choose "Open Preferences"

2. Select **AWS SETTINGS** and turn off **AWS managed temporary credentials**

3. Close the Preferences tab
   ![cloud9-config](/images/c9disableiam.png)

4. Copy and run (paste with **Ctrl+P**) the commands below.
   Before running it, review what it does by reading through the comments.


      ```sh
      sudo pip install --upgrade awscli && hash -r
      sudo apt update
      sudo apt install jq gettext bash-completion moreutils -y
      rm -vf ${HOME}/.aws/credentials
      export ACCOUNT_ID=$(aws sts get-caller-identity --output text --query Account)
      export STS_RESPONSE=$(aws sts assume-role --role-arn arn:aws:iam::${ACCOUNT_ID}:role/Docker-Workshop-Admin --role-session-name $(uuidgen) --duration-seconds 3600)
      export AWS_ACCESS_KEY_ID=$(echo $STS_RESPONSE | jq .Credentials.AccessKeyId | tr -d \")
      export AWS_SECRET_ACCESS_KEY=$(echo $STS_RESPONSE | jq .Credentials.SecretAccessKey | tr -d \")
      export AWS_SESSION_TOKEN=$(echo $STS_RESPONSE | jq .Credentials.SessionToken | tr -d \")
      export AWS_DEFAULT_REGION=us-east-1
      
      ```