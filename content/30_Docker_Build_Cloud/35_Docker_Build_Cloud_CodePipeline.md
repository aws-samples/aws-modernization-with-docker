---
title: "Docker Build Cloud – CodePipeline Perspective"
chapter: false
weight: 35
---

# 🔄 Docker Build Cloud – CodePipeline Perspective

Now that we've set up **Docker Build Cloud**, let’s integrate it into a **CI/CD pipeline** using **AWS CodePipeline** and **AWS CodeBuild**.  

This section will:  
✅ Securely **store Docker credentials** in AWS Secrets Manager  
✅ **Automate builds** using AWS CodeBuild  
✅ **Set up AWS CodePipeline** to manage builds and deployments  

---

## **1️⃣ Secure Credential Management with AWS Secrets Manager**
Before setting up the pipeline, we need to **store Docker Hub credentials securely**.

### **🔑 Store Docker Credentials**
Run the following commands to **enter your Docker credentials** and store them securely in AWS Secrets Manager:

```bash
read -p "Enter your Docker Hub username: " DOCKER_USERNAME
read -s -p "Enter your Docker Hub access token: " DOCKER_TOKEN
echo

aws secretsmanager create-secret --name dockerhub-credentials \
  --description "Docker Hub credentials for CI/CD pipeline" \
  --secret-string "{\"DOCKER_USERNAME\":\"$DOCKER_USERNAME\", \"DOCKER_TOKEN\":\"$DOCKER_TOKEN\"}"
```

✅ This command securely stores **DOCKER_USERNAME** and **DOCKER_TOKEN** in **AWS Secrets Manager**.  

### **✅ Verify Secret Creation**
Run the following to confirm the secret exists:

```bash
aws secretsmanager list-secrets --query "SecretList[?Name=='dockerhub-credentials']"
```

---

## **2️⃣ Understanding `buildspec.yml`**
The **`buildspec.yml`** file is a **YAML configuration file** that tells **AWS CodeBuild** what steps to take during a build. It includes:

✅ **Environment setup** (e.g., Secrets Manager for credentials)  
✅ **Pre-Build commands** (Logging into Docker Hub, setting up BuildKit)  
✅ **Build commands** (Building the Docker image but not pushing it)  
✅ **Artifacts** (Files that CodePipeline should store after the build)  

---

### **🔹 Key Sections in `buildspec.yml`**
Each `buildspec.yml` file follows this structure:

| Section | Purpose |
|---------|---------|
| `version` | Specifies the buildspec format version (use `0.2`). |
| `env` | Defines environment variables (e.g., **AWS Secrets Manager credentials**). |
| `phases.pre_build` | Commands to run **before** building the application (e.g., **log into Docker Hub**). |
| `phases.build` | Commands to **compile and package** the application (e.g., **build the Docker image**). |
| `artifacts` | Defines which files should be **saved** after the build (e.g., `imagedefinitions.json` for ECS). |

---

### **📄 `buildspec.yml` Explained**
Now, let’s walk through the **actual `buildspec.yml` file** step by step.

#### **1️⃣ Version & Environment Variables**
```yaml
version: 0.2
env:
  secrets-manager:
    DOCKER_USERNAME: "dockerhub-credentials:DOCKER_USERNAME"
    DOCKER_TOKEN: "dockerhub-credentials:DOCKER_TOKEN"
```
🔹 **What’s happening?**  
- We use **version 0.2** (latest format).  
- AWS **Secrets Manager** dynamically injects **Docker Hub credentials** so they don’t appear in plaintext.

---

#### **2️⃣ Pre-Build Phase**
```yaml
phases:
  pre_build:
    commands:
      - echo Logging in to Docker Hub...
      - echo $DOCKER_TOKEN | docker login -u $DOCKER_USERNAME --password-stdin
      - echo Setting up Docker Buildx...
      - docker buildx create --name mybuilder --use
      - docker buildx inspect --bootstrap
```
🔹 **What’s happening?**  
✅ **Authenticate to Docker Hub** using **AWS Secrets Manager**.  
✅ **Enable BuildKit** to allow **faster, more efficient Docker builds**.  

---

#### **3️⃣ Build Phase**
```yaml
  build:
    commands:
      - echo Building Docker image using BuildKit...
      - docker buildx build --platform linux/amd64,linux/arm64 -t $DOCKER_USERNAME/myapp:latest --load
```
🔹 **What’s happening?**  
✅ Uses **Docker Buildx** to create **multi-platform images**.  
✅ **Does NOT push** the image (pushing happens in **Docker Hub section**).  

---

#### **4️⃣ Artifacts**
```yaml
artifacts:
  files:
    - '**/*'
```
🔹 **What’s happening?**  
✅ Saves all build files in the **artifact store** (useful for later steps).  

---

## **3️⃣ Understanding `pipeline.yml`**
The **`pipeline.yml`** file is an **AWS CodePipeline configuration file** that automates the **CI/CD workflow**.

✅ **Defines pipeline stages** (Source, Build, Deploy)  
✅ **Uses GitHub as the source repository**  
✅ **Triggers AWS CodeBuild for Docker image builds**  

---

### **🔹 Key Sections in `pipeline.yml`**
Each `pipeline.yml` file follows this structure:

| Section | Purpose |
|---------|---------|
| `Resources.MyPipeline` | Defines the AWS CodePipeline setup. |
| `Stages.Source` | Fetches the latest code from **GitHub**. |
| `Stages.Build` | Runs AWS **CodeBuild** to build the application. |

---

### **📄 `pipeline.yml` Explained**
Now, let’s walk through the **actual `pipeline.yml` file** step by step.

#### **1️⃣ Source Stage**
```yaml
- Name: Source
  Actions:
    - Name: GitHubSource
      ActionTypeId:
        Category: Source
        Owner: ThirdParty
        Provider: GitHub
        Version: "1"
      Configuration:
        Owner: "REPLACE_WITH_YOUR_GITHUB_USERNAME"
        Repo: "REPLACE_WITH_YOUR_GITHUB_REPO"
        Branch: "main"
        OAuthToken: "{{resolve:secretsmanager:GitHub/Token}}"
      OutputArtifacts:
        - Name: SourceArtifact
```
🔹 **What’s happening?**  
✅ Pulls the **latest source code** from GitHub.  
✅ Uses **AWS Secrets Manager** to retrieve the **GitHub OAuth Token** securely.

---

#### **2️⃣ Build Stage**
```yaml
- Name: Build
  Actions:
    - Name: BuildDockerImage
      ActionTypeId:
        Category: Build
        Owner: AWS
        Provider: CodeBuild
        Version: "1"
      Configuration:
        ProjectName: docker-build-cloud-project
      InputArtifacts:
        - Name: SourceArtifact
      OutputArtifacts:
        - Name: BuildOutput
```
🔹 **What’s happening?**  
✅ Runs AWS **CodeBuild** to execute `buildspec.yml`.  
✅ Builds the **Docker image** but does not push it yet.

---

## **✅ Next Steps**
1️⃣ **Run the AWS CLI commands** to store credentials securely.  
2️⃣ **Generate the `buildspec.yml` and `pipeline.yml`** automatically.  
3️⃣ **Manually edit `pipeline.yml`** in VS Code.  
4️⃣ **In the next section (`Docker Hub`), we will push the built image.**  

🚀 Once this is done, you’ll have a fully automated **Docker Build Cloud CI/CD pipeline** set up in **AWS CodePipeline**.  
