+++
title = "Step 2: Introducion to AWS CodeBuild and AWS CodePipeline"
chapter = false
weight = 21
+++

In this section we will set up our CI/CD pipline using AWS CodeBuild and AWS CodePipeline as our tools of choice. We will also use CloudFormation to deploy our pipeline. Let's go ahead and give a quick overview of both AWS CodePipeline and AWS CodeBuild to give us a better understanding of how both services play a part if building our pipeline. 

### Deploy CI/CD Pipeline with CloudFormation
We will be using CloudFormation in order to set up our CI/CD pipeline so copy and paste the following command in your terminal. We have included the configuration file for the CloudFormation API's to reference. This will take 1-2 minutes to deploy. 
```
aws cloudformation create-stack --stack-name docker-compose-code-pipeline --template-body file://operations/code-pipeline-cloudformation.yaml --capabilities CAPABILITY_NAMED_IAM --enable-termination-protection --region us-east-1 --parameters DockerPullSecretsManagerArn=${DOCKER_PULL_SECRETS_MANAGER} GitHubOwner='your GitHub username'
```

Once our CloudFormation is done deploying, we should see the following in the AWS console
![Docker](/images/CFN.png)

# AWS CodeBuild
At the start of this section we defined the differences between CI and CD and how they can be used together to bring automation to your development lifecycle. AWS CodeBuild is considered the CI component for our pipeline. AWS CodeBuild is a fully managed continuous integration service that compiles source code, runs tests, and produces software packages that are ready to deploy. With CodeBuild, you don’t need to provision, manage, and scale your own build servers. CodeBuild scales continuously and processes multiple builds concurrently, so your builds are not left waiting in a queue. You can get started quickly by using prepackaged build environments, or you can create custom build environments that use your own build tools. With CodeBuild, you are charged by the minute for the compute resources you use.

# AWS CodePipeline
AWS CodePipeline is a fully managed **continuous delivery** service that helps you automate your release pipelines for fast and reliable application and infrastructure updates. CodePipeline automates the build, test, and deploy phases of your release process every time there is a code change, based on the release model you define. This enables you to rapidly and reliably deliver features and updates. You can easily integrate AWS CodePipeline with third-party services such as GitHub or with your own custom plugin. With AWS CodePipeline, you only pay for what you use. There are no upfront fees or long-term commitments.

Now that our CI/CD pipeline is set up, let's break down what our pipeline is doing and what resources our pipeline depends on to run properly. 

Let's take a look at the following portion of our CloudFormation template:
```
  CodeBuildProject:
    Type: 'AWS::CodeBuild::Project'
    Properties:
      Name: !Ref 'AWS::StackName'
      ServiceRole: !GetAtt 
        - CodeBuildServiceRole
        - Arn
      Source:
        Type: GITHUB
        Location: !Sub 'https://github.com/${GitHubOwner}/${GitHubRepository}.git'
        BuildSpec: buildspec.yaml
        Auth:
          Type: OAUTH
          Resource: !Ref CodeBuildSourceCredential
      Artifacts:
        Type: NO_ARTIFACTS
      Triggers:
        Webhook: true
        FilterGroups:
          - - Type: EVENT
              Pattern: >-
                PULL_REQUEST_CREATED, PULL_REQUEST_UPDATED,
                PULL_REQUEST_REOPENED, PULL_REQUEST_MERGED,
            - Type: BASE_REF
              Pattern: !Sub '^refs/heads/${GitHubBranch}$'
          - - Type: EVENT
              Pattern: >-
                PUSH
            - Type: HEAD_REF
              Pattern: '^refs/tags/.*'
      Environment:
        Type: LINUX_CONTAINER
        ComputeType: BUILD_GENERAL1_SMALL
        Image: !Ref CodeBuildEnvironmentImage
        EnvironmentVariables:
          - Name: DOCKERHUB_USERNAME
            Type: SECRETS_MANAGER 
            Value: $DOCKER_PULL_SECRETS_MANAGER
          - Name: DOCKERHUB_PASSWORD
            Type: SECRETS_MANAGER
            Value: $DOCKER_PULL_SECRETS_MANAGER
```

## CodeBuild
Under the source we specify GitHub as this is where our source code and buildspec files live. We could theoretically use S3 or another source repository as well to store our source code and buildspec files but this would require additional configuration that is out of scope for this workshop. You can also see that we include authentication to our GitHub repository through our secret created in AWS Secrets manager that we created in the previous step. 

You might also notice that there is a Triggers section and what this means is that we have defined in our CloudFormation template specific GitHub events that will trigger CodeBuild to build an updated version of our application. We currently have it set to trigger CodeBuild through push and pull request events in GitHub. The nice part about this is that you can customize the events to your specific needs but for this workshop we kept it to push and pull request events. 

The last piece to mention is that you may be wondering how CodeBuild is accessing our Docker Hub repository but if you remember from Module 1, we also created a secret in Secrets Manager with our GitHub credentials as well. We pass that through environment variables that make API calls to Secrets Manager whenever our CodeBuild is triggered by a GitHub event. 

Once CodeBuild finishes building our application, you'll see that it creates a folder in our S3 bucket with our source code bundled up as a zip file. In the next section we will show you what your S3 bucket should look like once CodeBuild finishes building from your source code. 

## Buildspec Build and Deploy
You will also notice under the /operations folder that there are two buildspec.yaml files. The buildspec.yaml is there to build our Docker image and push that image to Docker Hub. The deploysepc.yaml is there to take the Docker image and deploy any changes to the application to ECS. Let's break down each file so we understand what is going on in the background every time we push a change to your source code in GitHub. 

```
version: 0.2
phases:
  install:
    runtime-versions:
      python: 3.8
    commands:
      - echo "Performing manual install of compose cli"
      - curl -L -o docker-linux-amd64.tar.gz https://github.com/docker/compose-cli/releases/download/v1.0.10/docker-linux-amd64.tar.gz
      - tar xzf docker-linux-amd64.tar.gz
      - chmod +x docker/docker
      - ls -ltr
      - docker/docker compose --help
      - which docker
      - ln -s $(which docker) /usr/local/bin/com.docker.cli
  pre_build:
    commands:
      - echo Logging in to Docker Hub...
      - docker login --username $DOCKERHUB_USERNAME --password $DOCKERHUB_PASSWORD

  build:
    commands:
      - echo Build started on `date`
      - docker/docker compose build
      - echo "Tagging Docker image for Docker Hub"
      - docker images
      - docker tag src_backend:latest ${DOCKERHUB_USERNAME}/docker-compose-ecs-sample_backend:${CODEBUILD_RESOLVED_SOURCE_VERSION}
      - docker tag src_frontend:latest ${DOCKERHUB_USERNAME}/docker-compose-ecs-sample_frontend:${CODEBUILD_RESOLVED_SOURCE_VERSION}
      - docker push ${DOCKERHUB_USERNAME}/docker-compose-ecs-sample_backend:${CODEBUILD_RESOLVED_SOURCE_VERSION}
      - docker push ${DOCKERHUB_USERNAME}/docker-compose-ecs-sample_frontend:${CODEBUILD_RESOLVED_SOURCE_VERSION}
  
  post_build:
    commands:
      - echo "build successful"
```

The buildspec.yaml file is broken down into phases and before any code is built, we include a step to install the latest version of Docker that includes the ECS integration that we need for our application. The next step includes logging into Docker Hub, create the ECS context using the Docker context command, and will build our Docker Compose image. Once the image is built, the file will then push that image to Docker Hub. 


```
version: 0.2
phases:
  install:
    runtime-versions:
      python: 3.8
    commands:
      - echo "Performing manual install of compose cli"
      - curl -L -o docker-linux-amd64.tar.gz https://github.com/docker/compose-cli/releases/download/v1.0.10/docker-linux-amd64.tar.gz
      - tar xzf docker-linux-amd64.tar.gz
      - chmod +x docker/docker
      - ls -ltr
      - docker/docker compose --help
      - which docker
      - ln -s $(which docker) /usr/local/bin/com.docker.cli
  pre_build:
    commands:
      - echo Logging in to Docker Hub...
      - docker login --username $DOCKERHUB_USERNAME --password $DOCKERHUB_PASSWORD
      - STS_RESPONSE=$(curl 169.254.170.2$AWS_CONTAINER_CREDENTIALS_RELATIVE_URI)
      - export AWS_ACCESS_KEY_ID=$(echo $STS_RESPONSE | jq .AccessKeyId | tr -d \")
      - export AWS_SECRET_ACCESS_KEY=$(echo $STS_RESPONSE | jq .SecretAccessKey | tr -d \")
      - export AWS_SESSION_TOKEN=$(echo $STS_RESPONSE | jq .Token | tr -d \")
      - echo "Create Docker ECS context"
      - docker/docker context create ecs ecs-workshop --from-env
      - aws sts get-caller-identity
      - echo "Change context to use ECS context"
      - docker/docker context use ecs-workshop

  build:
    commands:
      - DOCKER_HUB_ID=${DOCKERHUB_USERNAME} DOCKER_PULL_SECRETS_MANAGER=${DOCKER_PULL_SECRETS_MANAGER} docker/docker compose -f docker-compose.yaml -f  docker-compose.prod.migrate.yaml -p ${PROJECT_NAME} up
  
  post_build:
    commands:
      - echo "deploy successful"
```
The deployspec.yaml file is similar to our buildspec.yaml file but the main difference here is that it takes the image that is pushed to Docker Hub and then deploys it to ECS as you can see under the build phase of the file. If you also remember from Module 2, once you set your Docker context to ECS we can simply run the docker compose up command and it will take all the resources defined in the docker-compose files and deploy our application to ECS along with creating other AWS resources that we have defined as well. 

In the next section we will push a change to our source code to demonstrate the concepts covered in this section along with showing how CodePipeline orchestrates our deployment pipeline. 
