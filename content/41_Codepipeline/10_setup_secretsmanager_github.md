+++
title = "Step 1: Create Secret with AWS Secrets Manager"
chapter = false
weight = 21
+++

Since we are using GitHub to store all of our code assets, in order for us to use GitHub with our CI/CD pipeline we need to authorize CodePipeline to use GitHub as its source. In this section we will teach you how to store a GitHub OAuth token in Secrets Manager for us to use later in this module. 


Let's start off by generating a token for us to use in GitHub. 
(insert image)

The next step is to create a webhook in our GitHub repository so that we can have any push events to kick off our CI/CD pipeline. The only options that you need to select is repo and admin:repo_hook. 
(insert image) 

```
aws secretsmanager create-secret --name github-oauth-token
          --description "Secret for GitHub" --secret-string "insert your GitHub OAuth token"
```

You should see a similar output if the command was able to successfully run.
```
{
    "ARN": "arn:aws:secretsmanager:us-west-2:123456789012:secret:tutorials/MyFirstSecret-rzM8Ja",
    "Name": "MyFirstSecret",
    "VersionId": "35e07aa2-684d-42fd-b076-3b3f6a19c6dc"
}
```