---
title : "Connecting GitHub to AWS CodePipeline via CodeStar"
weight : 81
---

**📌 Setting Up GitHub CodeStar Connection via AWS CLI**
To integrate AWS CodePipeline with GitHub using **AWS CodeStar Connections**, follow these **CLI-based instructions**.
**1️⃣ Create the CodeStar Connection (CLI)**
Run the following command to create a **new GitHub CodeStar Connection**:

# Create the connection and save the ARN to a variable in one step
CONNECTION_ARN=$(aws codestar-connections create-connection \
--provider-type GitHub \
--connection-name my-github-connection \
--query 'ConnectionArn' \
--output text)

# Verify the connection ARN was captured correctly
echo $CONNECTION_ARN

**✅ Expected Output**
arn:aws:codestar-connections:us-west-2:123456789012:connection/your-connection-id

**2️⃣ Authorize the Connection (Manual Step)**
The **final authorization step must be completed in the AWS Console**.
📌 **Click this link to finalize the authorization** (replace the region if needed):
🔗 **Complete GitHub Connection Authorization**
1. Locate the **newly created connection**.
2. Click **"Update pending connection"**.
3. Follow the prompts to **authenticate with GitHub**.
4. **IMPORTANT:** When asked which repositories to connect, select the specific "Rent-A-Room" repository you forked (your-github-username/Rent-A-Room) instead of granting access to all repositories.
5. Once done, the **status should change to **`Available`.
**3️⃣ Deploy CloudFormation with GitHub Connection**
After authorization, use the saved variable to deploy your CloudFormation stack:

# Prompt for your GitHub username (case sensitive)
read -p "Enter your GitHub username (case sensitive): " GITHUB_USERNAME

aws cloudformation deploy --template-file ecs-pipeline-setup.yaml --stack-name ECSPipelineStack \
--parameter-overrides \
GitHubOwner="$GITHUB_USERNAME" \
GitHubRepo="Rent-A-Room" \
GitHubBranch="main" \
CodeStarConnectionArn="$CONNECTION_ARN"

**✅ Summary**
🚀 **Step 1:** Create a GitHub CodeStar Connection via AWS CLI and save ARN to variable
🔗 **Step 2:** Click the link & authorize GitHub manually in AWS Console, selecting only the "Rent-A-Room" repository
📦 **Step 3:** Enter your GitHub username (case sensitive) when prompted and deploy CloudFormation
This ensures **secure authentication** between AWS CodePipeline and GitHub without storing **OAuth tokens** manually.
