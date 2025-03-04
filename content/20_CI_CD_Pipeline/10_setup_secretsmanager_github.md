---
title: "Step 1: Add GitHub credentials to AWS Secrets Manager"
chapter: false
weight: 21
---

We will be using GitHub to store all of our code assets and in order for us to use GitHub with our CI/CD pipeline we need to authorize CodePipeline and CodeBuild to use GitHub as its source to kick off our build. In this section we will teach you how to store a GitHub OAuth token in Secrets Manager for us to use later in this module. 


Let's start off by generating a token for us to use in GitHub. On the right hand corner of the GitHub page, click on your display photo and click on settings. 
![Docker](/images/github-steps.png)

From there you will look for a section called **Developer Settings** and click on **Personal Access Tokens**. We will need to generate a new token so go ahead and click on **Generate new token** and select the following checkboxes. We only need to give access to our repository and to give permissions for webhooks that use our repository so your screen should look like the following. If that all looks correct, click generate token. 
![Docker](/images/github-steps2.png)

You will now see your new token so please copy and paste that token to a notepad or anywhere that you can reference it for the next step. 

We will be using AWS Secrets Manager to store our GitHub access token so that our CodeBuild project will be able to reference our credentials and have the ability to use our GitHub repository as its source. 

```
aws secretsmanager create-secret --name github-oauth-token
          --description "Secret for GitHub" --secret-string "insert your GitHub OAuth token"
```

You should see a similar output if the command was able to successfully run.
```
{
    "ARN": "arn:aws:secretsmanager:us-west-2:123456789012:secret:tutorials/MyFirstSecret-rzM8Ja",
    "Name": "github-oauth-token",
    "VersionId": "35e07aa2-684d-42fd-b076-3b3f6a19c6dc"
}
```

If you browse to the AWS Secrets Manager console, you should see two secrets now:

1. Secret that holds our credentials for Docker Hub
2. Secret that holds our credentials for Github

![Docker](/images/secrets-manager.png)

In the next step we will give an overview of AWS CodeBuild and AWS CodePipeline along with deploying our pipeline using a CloudFormation template created by the AWS team. 
