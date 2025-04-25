---
title: "Docker Build Cloud – CodePipeline Perspective"
chapter: false
weight: 82
---

# 🔄 Docker Build Cloud – CodePipeline Perspective

Now that we've set up **Docker Build Cloud**, let's integrate it into a **CI/CD pipeline** using **AWS CodePipeline** and **AWS CodeBuild**.  

This section will:  
✅ **Automating builds** using AWS CodeBuild  
✅ **Set up AWS CodePipeline** to manage builds and deployments  

---

## **1️⃣ Understanding `buildspec.yml`**

Run this command below to create your buildspec file.

```bash
cat <<EOF > buildspec.yml
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
      - docker buildx build --platform linux/amd64,linux/arm64 -t $DOCKER_USERNAME/rent-a-room:latest --load .

artifacts:
  files:
    - '**/*'
EOF
```

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
Now, let's walk through the **actual `buildspec.yml` file** step by step.

#### **1️⃣ Version & Environment Variables**
```yaml
version: 0.2
env:
  secrets-manager:
    DOCKER_USERNAME: "dockerhub-credentials:DOCKER_USERNAME"
    DOCKER_TOKEN: "dockerhub-credentials:DOCKER_TOKEN"
```
🔹 **What's happening?**  
- We use **version 0.2** (latest format).  
- AWS **Secrets Manager** dynamically injects **Docker Hub credentials** so they don't appear in plaintext.

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
🔹 **What's happening?**  
✅ **Authenticate to Docker Hub** using **AWS Secrets Manager**.  
✅ **Enable BuildKit** to allow **faster, more efficient Docker builds**.  

---

#### **3️⃣ Build Phase**
```yaml
  build:
    commands:
      - echo Building Docker image using BuildKit...
      - docker buildx build --platform linux/amd64,linux/arm64 -t $DOCKER_USERNAME/rent-a-room:latest --load .
```
🔹 **What's happening?**  
✅ Uses **Docker Buildx** to create **multi-platform images**.  
✅ **Does NOT push** the image (pushing happens in **Docker Hub section**).  

---

#### **4️⃣ Artifacts**
```yaml
artifacts:
  files:
    - '**/*'
```
🔹 **What's happening?**  
✅ Saves all build files in the **artifact store** (useful for later steps).  

---

## **2️⃣ Understanding `pipeline.yml`**
The **`pipeline.yml`** file defines the **AWS CodePipeline configuration** to automate the **CI/CD workflow**. Unlike a CloudFormation template, this file uses the direct pipeline format which can be used with the AWS CLI to create or update a pipeline.

✅ **Defines pipeline stages** (Source, Build, Deploy)  
✅ **Uses GitHub as the source repository**  
✅ **Triggers AWS CodeBuild for Docker image builds**  

Here is the complete **`pipeline.yml`** in direct pipeline format:

```yaml
pipeline:
  roleArn: arn:aws:iam::account-id:role/CodePipelineServiceRole
  stages:
    - name: Source
      actions:
        - name: GitHubSource
          actionTypeId:
            category: Source
            owner: ThirdParty
            provider: GitHub
            version: '1'
          configuration:
            Owner: GitHubOwner
            Repo: GitHubRepo
            Branch: main
            OAuthToken: '{{resolve:secretsmanager:GitHub/WorkshopOwnerToken:SecretString:OwnerToken}}'
          outputArtifacts:
            - name: SourceArtifact
          runOrder: 1
    
    - name: Build
      actions:
        - name: BuildDockerImage
          actionTypeId:
            category: Build
            owner: AWS
            provider: CodeBuild
            version: '1'
          configuration:
            ProjectName: docker-build-cloud-project
          inputArtifacts:
            - name: SourceArtifact
          outputArtifacts:
            - name: BuildOutput
          runOrder: 1
  
  artifactStore:
    type: S3
    location: codepipeline-artifact-bucket
  name: docker-build-cloud-pipeline
  version: 1
```

This format can be used with AWS CLI to create a pipeline with this command `aws codepipeline create-pipeline --cli-input-yaml file://pipeline.yml` but we are not going to create the pipeline just yet!

---

### **🔹 Key Sections in `pipeline.yml`**
The direct pipeline format follows this structure:

| Section | Purpose |
|---------|---------|
| `pipeline` | The root element that contains the entire pipeline definition. |
| `roleArn` | The IAM role that CodePipeline will use to execute the pipeline. |
| `stages` | An array of pipeline stages (e.g., Source, Build, Deploy). |
| `artifactStore` | Defines the S3 bucket where CodePipeline stores artifacts. |
| `name` | The name of your pipeline. |
| `version` | The version of the pipeline (usually 1). |

---

### **📄 `pipeline.yml` Explained**
Now, let's walk through the **actual `pipeline.yml` file** step by step.

#### **1️⃣ Source Stage**
```yaml
- name: Source
  actions:
    - name: GitHubSource
      actionTypeId:
        category: Source
        owner: ThirdParty
        provider: GitHub
        version: '1'
      configuration:
        Owner: GitHubOwner
        Repo: GitHubRepo
        Branch: main
        OAuthToken: '{{resolve:secretsmanager:GitHub/WorkshopOwnerToken:SecretString:OwnerToken}}'
      outputArtifacts:
        - name: SourceArtifact
      runOrder: 1
```
🔹 **What's happening?**

✅ Pulls the **latest source code** from GitHub.  
✅ Uses **AWS Secrets Manager** to retrieve the **GitHub OAuth Token** securely.  
✅ Specifies the GitHub repository owner and repository name.  
✅ Targets the 'main' branch of the repository.  
✅ Outputs the source code as an artifact named **SourceArtifact** for later stages.

---

#### **2️⃣ Build Stage**
```yaml
- name: Build
  actions:
    - name: BuildDockerImage
      actionTypeId:
        category: Build
        owner: AWS
        provider: CodeBuild
        version: '1'
      configuration:
        ProjectName: docker-build-cloud-project
      inputArtifacts:
        - name: SourceArtifact
      outputArtifacts:
        - name: BuildOutput
      runOrder: 1
```
🔹 **What's happening?**  

✅ Runs AWS CodeBuild to execute the buildspec.yml file in the source code.  
✅ Uses the CodeBuild project named "docker-build-cloud-project" for the build process.  
✅ Takes the **SourceArtifact** from the previous stage as input.  
✅ Builds the Docker image based on instructions in the buildspec, but does not push it yet.  
✅ Prepares the Docker image for later stages (like pushing to DockerHub or scanning with Docker Scout).  
✅ Saves the build output as **BuildOutput** artifact.

---

#### **3️⃣ ArtifactStore & Pipeline Properties**
```yaml
artifactStore:
  type: S3
  location: codepipeline-artifact-bucket
name: docker-build-cloud-pipeline
version: 1
```
🔹 **What's happening?**  

✅ Specifies an S3 bucket named "codepipeline-artifact-bucket" to store all pipeline artifacts.  
✅ Names the pipeline "docker-build-cloud-pipeline".  
✅ Sets the pipeline version to 1.

---

## **✅ Next Steps**

**In the next section (`Docker Hub`), we will push the built image to our DockerHub Repository.**  

🚀 Once this is done, you'll be a few stages away from a full **Docker Build Cloud CI/CD pipeline** set up in **AWS CodePipeline**.
