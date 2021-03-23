+++
title = "Step 2: Introducion to AWS CodeBuild and AWS CodePipeline"
chapter = false
weight = 21
+++

In this section we will set up our CI/CD pipline using AWS CodeBuild and AWS CodePipeline as our tools of choice. We will also use CloudFormation to deploy our pipeline. Let's go ahead and give a quick overview of both AWS CodePipeline and AWS CodeBuild to give us a better understanding of how both services play a part if building our pipeline. 

# AWS CodeBuild
In the previous section we defined the differences between CI and CD and how they can be used together to bring automation to your development lifecycle. AWS CodeBuild is considered the CI component for our pipeline. AWS CodeBuild is a fully managed continuous integration service that compiles source code, runs tests, and produces software packages that are ready to deploy. With CodeBuild, you don’t need to provision, manage, and scale your own build servers. CodeBuild scales continuously and processes multiple builds concurrently, so your builds are not left waiting in a queue. You can get started quickly by using prepackaged build environments, or you can create custom build environments that use your own build tools. With CodeBuild, you are charged by the minute for the compute resources you use.

# AWS CodePipeline
AWS CodePipeline is a fully managed **continuous delivery** service that helps you automate your release pipelines for fast and reliable application and infrastructure updates. CodePipeline automates the build, test, and deploy phases of your release process every time there is a code change, based on the release model you define. This enables you to rapidly and reliably deliver features and updates. You can easily integrate AWS CodePipeline with third-party services such as GitHub or with your own custom plugin. With AWS CodePipeline, you only pay for what you use. There are no upfront fees or long-term commitments.

### Deploy AWS CodeBuild and AWS CodePipeline with CloudFormation
We will be using CloudFormation in order to set up our CI/CD pipeline so copy and paste the following command in your terminal. We have included the configuration file for the CloudFormation API's to reference. This will take 1-2 minutes to deploy. 
```
aws cloudformation create-stack --stack-name MY-Website-Pipeline --template-body file://docker-pipeline.yaml --capabilities CAPABILITY_NAMED_IAM --enable-termination-protection
```

Once our CloudFormation is done deploying, we should see the following in the AWS console
(insert image)

Now that our CI/CD pipeline is set up, let's break down what our pipeline is doing and what resources our pipeline depends on to run properly. 

1.