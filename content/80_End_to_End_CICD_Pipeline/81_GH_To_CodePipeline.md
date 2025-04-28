---
title: "Connecting GitHub to AWS CodePipeline via CodeStar"
chapter: false
weight: 81
---

# 🔗 Setting Up GitHub CodeStar Connection

## 📋 Overview

AWS CodeStar Connections provides a secure way to connect your GitHub repositories to AWS CodePipeline without storing OAuth tokens. This modern approach:

- ✅ **Improves security** by eliminating the need for personal access tokens
- ✅ **Simplifies management** with centralized connection handling
- ✅ **Enables fine-grained access control** to specific repositories

## 🛠️ Step-by-Step Implementation

### 1️⃣ Create the CodeStar Connection (CLI)

Run the following command to create a new GitHub CodeStar Connection and save the ARN to a variable:

```bash
# Create the connection and save the ARN to a variable
CONNECTION_ARN=$(aws codestar-connections create-connection \
--provider-type GitHub \
--connection-name my-github-connection \
--query 'ConnectionArn' \
--output text)

# Verify the connection ARN was captured correctly
echo $CONNECTION_ARN

# Save the connection ARN to a file for later use
echo "export CONNECTION_ARN=$CONNECTION_ARN" > connection-config.sh
```

**✅ Expected Output:**
```
arn:aws:codestar-connections:us-west-2:123456789012:connection/your-connection-id
```

### 2️⃣ Authorize the Connection (Manual Step)

The final authorization step must be completed in the AWS Console:

1. Navigate to the [Developer Tools > Connections](https://console.aws.amazon.com/codesuite/settings/connections) section in the AWS Console
2. Locate your **newly created connection** (named "my-github-connection")
3. Click **"Update pending connection"**
4. Follow the prompts to **authenticate with GitHub**
5. **IMPORTANT:** When asked which repositories to connect, select the specific "Rent-A-Room" repository you forked (`your-github-username/Rent-A-Room`) instead of granting access to all repositories
6. Once completed, the status should change to **`Available`**

![CodeStar Connection](/images/codestar-connection.png)

### 3️⃣ Get Your GitHub Username Automatically

Let's use the GitHub CLI to get your actual GitHub username:

```bash
# Get your GitHub username using GitHub CLI
GITHUB_USERNAME=$(gh api user -q '.login')
echo "Your GitHub Username: $GITHUB_USERNAME"

# Verify this is correct
read -p "Is this your GitHub username? (y/n): " CONFIRM
if [[ $CONFIRM != "y" && $CONFIRM != "Y" ]]; then
  read -p "Please enter your GitHub username: " GITHUB_USERNAME
fi

# Set repository and branch information
GITHUB_REPO="Rent-A-Room"
GITHUB_BRANCH="main"

echo "GitHub Username: $GITHUB_USERNAME"
echo "GitHub Repository: $GITHUB_REPO"
echo "GitHub Branch: $GITHUB_BRANCH"
```

### 4️⃣ Create the buildspec.yml File

Now, let's create the buildspec.yml file that will be used by AWS CodeBuild to build our Docker images with multi-architecture support:

```bash
cat << 'EOF' > buildspec.yml
version: 0.2

env:
  secrets-manager:
    DOCKER_USERNAME: "dockerhub-credentials:DOCKER_USERNAME"
    DOCKER_TOKEN: "dockerhub-credentials:DOCKER_TOKEN"

phases:
  pre_build:
    commands:
      # Print Docker version for debugging
      - docker --version
      - echo Logging in to Docker Hub...
      - echo $DOCKER_TOKEN | docker login -u $DOCKER_USERNAME --password-stdin
      
      # Install Docker Buildx
      - echo Installing Docker Buildx...
      - export DOCKER_BUILDX_VERSION=v0.11.2
      - mkdir -p ~/.docker/cli-plugins
      - curl -sSL https://github.com/docker/buildx/releases/download/${DOCKER_BUILDX_VERSION}/buildx-${DOCKER_BUILDX_VERSION}.linux-amd64 -o ~/.docker/cli-plugins/docker-buildx
      - chmod +x ~/.docker/cli-plugins/docker-buildx
      - docker buildx version
      
      # Set up Docker Buildx
      - docker buildx create --name mybuilder --use
      - docker buildx inspect --bootstrap
      
      # Capturing build info
      - COMMIT_HASH=$(echo $CODEBUILD_RESOLVED_SOURCE_VERSION | cut -c 1-7)
      - IMAGE_TAG=${COMMIT_HASH:=latest}
      - echo Build started on `date`
      - echo Building image with tag $IMAGE_TAG
      
      # Enable BuildKit
      - export DOCKER_BUILDKIT=1
  
  build:
    commands:
      - echo Building Docker image for ARM64 architecture using Buildx...
      - docker buildx build --platform linux/arm64 -t $DOCKER_USERNAME/rent-a-room:latest -t $DOCKER_USERNAME/rent-a-room:$IMAGE_TAG --load .
  
  post_build:
    commands:
      - echo Build completed on `date`
      - echo Pushing Docker image to Docker Hub...
      - docker push $DOCKER_USERNAME/rent-a-room:latest
      - docker push $DOCKER_USERNAME/rent-a-room:$IMAGE_TAG
      - echo Writing image definitions file...
      - printf '[{"name":"rent-a-room","imageUri":"%s/rent-a-room:%s"}]' $DOCKER_USERNAME $IMAGE_TAG > imagedefinitions.json
      - cat imagedefinitions.json

artifacts:
  files:
    - imagedefinitions.json
EOF
```

## 🔍 How It Works

1. **Connection Creation**: AWS generates a unique connection identifier
2. **Manual Authorization**: You authenticate with GitHub and grant specific permissions
3. **Username Retrieval**: GitHub CLI retrieves your actual GitHub username
4. **Connection Storage**: AWS securely stores the connection details
5. **Pipeline Integration**: The connection ARN and your GitHub information are used in your pipeline configuration

## ⚠️ Troubleshooting

If you encounter issues:

- **Lost Connection ARN**: If you lose your terminal session or the CONNECTION_ARN variable, you can retrieve it with:
  ```bash
  # List all connections
  aws codestar-connections list-connections --query "Connections[?ConnectionName=='my-github-connection'].ConnectionArn" --output text
  
  # Save it to the variable again
  CONNECTION_ARN=$(aws codestar-connections list-connections --query "Connections[?ConnectionName=='my-github-connection'].ConnectionArn" --output text)
  
  # Update the config file
  echo "export CONNECTION_ARN=$CONNECTION_ARN" > connection-config.sh
  ```

- Ensure your GitHub account has admin access to the repository
- Check that the connection status shows as **Available** in the AWS Console
- Verify that your AWS CLI has the correct permissions to create connections
- If GitHub CLI commands fail, ensure you're authenticated with `gh auth login`
- If you get an error about the repository not being found, verify that you've forked the Rent-A-Room repository to your GitHub account

## ✅ Summary

🚀 **Step 1:** Create a GitHub CodeStar Connection via AWS CLI and save ARN to variable  
🔗 **Step 2:** Authorize the connection manually in AWS Console, selecting only the "Rent-A-Room" repository  
🔍 **Step 3:** Automatically retrieve your GitHub username using GitHub CLI  
📦 **Step 4:** Deploy CloudFormation with your GitHub information and connection ARN

This approach ensures secure authentication between AWS CodePipeline and GitHub without storing OAuth tokens manually, following AWS security best practices.

In the next section, we'll configure the Docker Build Cloud integration with CodePipeline to automate our container builds.
