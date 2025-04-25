---
title: "Understanding the Pipeline Configuration"
chapter: false
weight: 82
---

# 🔄 Understanding the CI/CD Pipeline Configuration

Now that we've set up the GitHub connection, let's understand how our CI/CD pipeline is configured to integrate Docker Build Cloud, Docker Scout, and Amazon ECS deployment.

## 📋 Pipeline Architecture Overview

Our CloudFormation template creates a complete CI/CD pipeline with these key components:

![pipeline-architecture](/images/pipeline-architecture.png)

1. **Source Stage**: Pulls code from your GitHub repository
2. **Build Stage**: Uses Docker Build Cloud for multi-architecture image building
3. **Security Scan Stage**: Integrates Docker Scout for vulnerability assessment
4. **Deploy Stage**: Deploys the application to Amazon ECS

## 🔍 Key Configuration Files

Let's examine the key configuration files that power our pipeline:

### 1️⃣ Understanding `buildspec.yml`

The **`buildspec.yml`** file tells **AWS CodeBuild** what steps to take during the build process:

```yaml
version: 0.2

env:
  secrets-manager:
    DOCKER_USERNAME: "dockerhub-credentials:DOCKER_USERNAME"
    DOCKER_TOKEN: "dockerhub-credentials:DOCKER_TOKEN"

phases:
  pre_build:
    commands:
      - echo Logging in to Docker Hub...
      - echo $DOCKER_TOKEN | docker login -u $DOCKER_USERNAME --password-stdin
      - echo Setting up Docker Buildx...
      - docker buildx create --name mybuilder --use
      - docker buildx inspect --bootstrap

  build:
    commands:
      - echo Building Docker image using BuildKit...
      - docker buildx build --platform linux/amd64,linux/arm64 -t $DOCKER_USERNAME/rent-a-room:latest --push .

  post_build:
    commands:
      - echo Running Docker Scout scan...
      - docker scout cves $DOCKER_USERNAME/rent-a-room:latest
      - echo Creating imagedefinitions.json file for ECS deployment...
      - echo '[{"name":"rent-a-room-container","imageUri":"'$DOCKER_USERNAME'/rent-a-room:latest"}]' > imagedefinitions.json

artifacts:
  files:
    - imagedefinitions.json
    - appspec.yaml
    - taskdef.json
```

#### 🔹 Key Sections in `buildspec.yml` Explained

| Section | Purpose | What It Does |
|---------|---------|-------------|
| `version` | Specifies format version | Uses version 0.2 of the buildspec format |
| `env.secrets-manager` | Securely retrieves credentials | Gets Docker Hub credentials from AWS Secrets Manager |
| `phases.pre_build` | Setup before building | Logs into Docker Hub and sets up Docker Buildx |
| `phases.build` | Main build process | Builds multi-architecture images and pushes to Docker Hub |
| `phases.post_build` | Actions after build | Runs Docker Scout security scan and prepares ECS deployment files |
| `artifacts` | Files to save | Saves files needed for ECS deployment |

### 2️⃣ CloudFormation Pipeline Definition

Our CloudFormation template defines the complete pipeline structure, including the CodeBuild project that uses the buildspec:

```yaml
# First, the CodeBuild project definition that references the buildspec
CodeBuildProject:
  Type: AWS::CodeBuild::Project
  Properties:
    Name: !Sub ${AWS::StackName}-DockerBuildProject
    ServiceRole: !GetAtt CodeBuildServiceRole.Arn
    Artifacts:
      Type: CODEPIPELINE
    Environment:
      Type: LINUX_CONTAINER
      ComputeType: BUILD_GENERAL1_SMALL
      Image: aws/codebuild/amazonlinux2-x86_64-standard:3.0
      PrivilegedMode: true
    Source:
      Type: CODEPIPELINE
      BuildSpec: buildspec.yml  # This references the buildspec.yml in your repository

# Then, the Pipeline that uses the CodeBuild project
Pipeline:
  Type: AWS::CodePipeline::Pipeline
  Properties:
    RoleArn: !GetAtt CodePipelineServiceRole.Arn
    ArtifactStore:
      Type: S3
      Location: !Ref ArtifactBucket
    Stages:
      - Name: Source
        Actions:
          - Name: Source
            ActionTypeId:
              Category: Source
              Owner: AWS
              Provider: CodeStarSourceConnection
              Version: '1'
            Configuration:
              ConnectionArn: !Ref CodeStarConnectionArn
              FullRepositoryId: !Sub ${GitHubOwner}/${GitHubRepo}
              BranchName: !Ref GitHubBranch
            OutputArtifacts:
              - Name: SourceCode
      
      - Name: Build
        Actions:
          - Name: BuildAndPush
            ActionTypeId:
              Category: Build
              Owner: AWS
              Provider: CodeBuild
              Version: '1'
            Configuration:
              ProjectName: !Ref CodeBuildProject  # References the CodeBuild project above
            InputArtifacts:
              - Name: SourceCode
            OutputArtifacts:
              - Name: BuildOutput
      
      - Name: Deploy
        Actions:
          - Name: DeployToECS
            ActionTypeId:
              Category: Deploy
              Owner: AWS
              Provider: ECS
              Version: '1'
            Configuration:
              ClusterName: !Ref ECSClusterName
              ServiceName: !Ref ECSServiceName
              FileName: imagedefinitions.json
            InputArtifacts:
              - Name: BuildOutput
```

#### 🔹 Pipeline Stages Explained

| Stage | Component | Purpose |
|-------|-----------|---------|
| **Source** | CodeStar Connection | Securely connects to GitHub and pulls your code |
| **Build** | CodeBuild Project | Builds Docker images using Docker Build Cloud |
| **Deploy** | ECS Deployment | Updates your ECS service with the new container image |

## 🔍 How the Buildspec Integrates with CodePipeline

The connection between the buildspec file and the pipeline is through the **CodeBuild project**:

1. The **buildspec.yml** file must be in the root of your GitHub repository
2. The **CodeBuild project** in the CloudFormation template references this file
3. When the pipeline runs, CodeBuild reads and executes the instructions in the buildspec

Let's create the buildspec.yml file in your repository:

```bash
# Navigate to your repository
cd Rent-A-Room

# Create the buildspec.yml file
cat << 'EOF' > buildspec.yml
version: 0.2

env:
  secrets-manager:
    DOCKER_USERNAME: "dockerhub-credentials:DOCKER_USERNAME"
    DOCKER_TOKEN: "dockerhub-credentials:DOCKER_TOKEN"

phases:
  pre_build:
    commands:
      - echo Logging in to Docker Hub...
      - echo $DOCKER_TOKEN | docker login -u $DOCKER_USERNAME --password-stdin
      - echo Setting up Docker Buildx...
      - docker buildx create --name mybuilder --use
      - docker buildx inspect --bootstrap

  build:
    commands:
      - echo Building Docker image using BuildKit...
      - docker buildx build --platform linux/amd64,linux/arm64 -t $DOCKER_USERNAME/rent-a-room:latest --push .

  post_build:
    commands:
      - echo Running Docker Scout scan...
      - docker scout cves $DOCKER_USERNAME/rent-a-room:latest
      - echo Creating imagedefinitions.json file for ECS deployment...
      - echo '[{"name":"rent-a-room-container","imageUri":"'$DOCKER_USERNAME'/rent-a-room:latest"}]' > imagedefinitions.json

artifacts:
  files:
    - imagedefinitions.json
    - appspec.yaml
    - taskdef.json
EOF

# Commit and push the buildspec file
git add buildspec.yml
git commit -m "Add buildspec.yml for AWS CodeBuild"
git push origin main
```

Now that you've added the buildspec.yml file to your repository, let's create the Docker Hub credentials secret that the buildspec references:

```bash
# Create a secret for Docker Hub credentials
aws secretsmanager create-secret \
    --name dockerhub-credentials \
    --description "Docker Hub credentials for CI/CD pipeline" \
    --secret-string "{\"DOCKER_USERNAME\":\"your-dockerhub-username\",\"DOCKER_TOKEN\":\"your-dockerhub-token\"}"
```

Replace `your-dockerhub-username` and `your-dockerhub-token` with your actual Docker Hub credentials.

## 💡 How Docker Technologies Integrate

The pipeline seamlessly integrates Docker technologies:

1. **Docker Build Cloud**: Used in the build phase for efficient multi-architecture builds
2. **Docker Scout**: Integrated in the post-build phase for security scanning
3. **Docker Images**: Pushed to Docker Hub and deployed to Amazon ECS

## 🚀 Next Steps

Now that you understand how the pipeline is configured and have added the necessary buildspec file, let's complete the workshop by:

1. Making a change to the application
2. Triggering the pipeline
3. Observing the build, security scan, and deployment process

In the next section, we'll make a personalized change to the application and watch our complete CI/CD pipeline in action!
