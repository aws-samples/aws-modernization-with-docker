---
title: "Automating Docker Hub Pushes"
chapter: true
weight: 43
---

# Automating Docker Hub Pushes with AWS CodePipeline

In this section, you'll learn how to automate Docker image builds and pushes to Docker Hub using AWS CodePipeline.

## 🛠️ CodeBuild Configuration

First, let's create a buildspec.yml file for CodeBuild:

Create the buildspec.yml file:

```bash
cat << 'EOF' > buildspec.yml
version: 0.2

phases:
  pre_build:
    commands:
      # Login to Docker Hub
      - echo Logging in to Docker Hub...
      - docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_TOKEN
      # Get commit hash for tagging
      - COMMIT_HASH=$(echo $CODEBUILD_RESOLVED_SOURCE_VERSION | cut -c 1-7)
      - IMAGE_TAG=${COMMIT_HASH:=latest}
  
  build:
    commands:
      # Build Docker image
      - echo Building the Docker image...
      - docker build -t $DOCKERHUB_USERNAME/workshop-app:$IMAGE_TAG .
  
  post_build:
    commands:
      # Push to Docker Hub
      - echo Pushing the Docker image...
      - docker push $DOCKERHUB_USERNAME/workshop-app:$IMAGE_TAG
      # Create latest tag
      - docker tag $DOCKERHUB_USERNAME/workshop-app:$IMAGE_TAG $DOCKERHUB_USERNAME/workshop-app:latest
      - docker push $DOCKERHUB_USERNAME/workshop-app:latest
EOF
```

## 🔑 Setting Up Secrets

Store your Docker Hub credentials securely:

1. Open [AWS Secrets Manager](https://console.aws.amazon.com/secretsmanager)

![Secrets Manager](/images/click-create-secret.png)

2. Create new secret
   - Secret Type: Other type of secret
   - Key/value pairs:
     - DOCKERHUB_USERNAME: Your Docker Hub username
     - DOCKERHUB_TOKEN: Your Docker Hub access token
   - Name: `dockerhub-credentials`
   - Click next and click Store

![Secrets Manager](/images/secrets-manager.png)

## 📋 Creating the Build Project

1. Go to [AWS CodeBuild](console.aws.amazon.com/codesuite/codebuild/)
2. Create build project:
   - Name: `docker-workshop-build`
   - Source: Your repository
   - Environment:
     - Use managed image
     - Operating System: Amazon Linux 2
     - Runtime: Standard
     - Image: aws/codebuild/amazonlinux2-x86_64-standard:4.0
   - Privileged: ✅ Enable this flag for Docker builds

![CodeBuild Setup](/images/codebuild-setup.png)

## 🔄 Setting Up CodePipeline

1. Create new pipeline:
   - Name: `docker-workshop-pipeline`
   - Source: Your repository
   - Build: Use the CodeBuild project we created

2. Add environment variables from Secrets Manager:
   - DOCKERHUB_USERNAME: From secrets
   - DOCKERHUB_TOKEN: From secrets

![Pipeline Setup](/images/pipeline-setup.png)

## 🔧 IAM Role Configuration

Ensure your CodeBuild role has these permissions:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "secretsmanager:GetSecretValue"
            ],
            "Resource": "arn:aws:secretsmanager:*:*dockerhub-credentials*"
        }
    ]
}
```

## ✅ Testing the Pipeline

1. Make a change to your repository
2. Commit and push
3. Watch the pipeline execute:
   - Source stage pulls changes
   - Build stage builds and pushes image
   - Check Docker Hub for new image

![Pipeline Execution](/images/pipeline-execution.png)

## 🔍 Troubleshooting

Common issues:
1. **Build Fails**:
   - Check Docker Hub credentials
   - Verify Secrets Manager access
   - Ensure Docker daemon is running

2. **Push Fails**:
   - Verify Docker Hub token permissions
   - Check network connectivity
   - Review Docker Hub quota limits

## 🎯 Next Steps

You can now:
1. Automatically build Docker images
2. Push to Docker Hub on commits
3. Maintain versioned images
4. Use latest image tags