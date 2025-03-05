---
title: "Docker Hub Integration"
chapter: false
weight: 43
---

# 🐳 Docker Hub Integration – CodePipeline Perspective

Now that we’ve built our Docker images using **Docker Build Cloud**, we need to **push them to Docker Hub**. This section will:
- ✅ **Append a `post_build` phase** to `buildspec.yml` to **automate pushing images**.
- ✅ **Provide a fully updated `buildspec.yml` file**.
- ✅ **Show an updated `pipeline.yml` configuration** to integrate with AWS CodePipeline.

---

## **1️⃣ Updating `buildspec.yml` to Push to Docker Hub**

In the previous section, **Docker Build Cloud** built the image but did **not push it**.  
Now, we’ll **add a `post_build` phase** to push the image to Docker Hub.

### **📌 Appending the `post_build` Phase**

Run the following command to append the **new `post_build` phase** **before** the `artifacts` section in `buildspec.yml`:

```bash
sed -i '/artifacts:/i\
  post_build:\
    commands:\
      - echo Build completed on `date`\
      - echo Pushing the Docker image to Docker Hub...\
      - docker push $DOCKER_USERNAME/myapp:latest\
' buildspec.yml
```

---

## **2️⃣ Verify the Changes**
Once the command runs, check the updated `buildspec.yml`:

```bash
cat buildspec.yml
```

---

## **3️⃣ Fully Updated `buildspec.yml`**
Here is what your **final `buildspec.yml`** should look like **after** adding Docker Hub push automation:

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
      - docker buildx build --platform linux/amd64,linux/arm64 -t $DOCKER_USERNAME/myapp:latest --load

  post_build:
    commands:
      - echo Build completed on `date`
      - echo Pushing the Docker image to Docker Hub...
      - docker push $DOCKER_USERNAME/myapp:latest

artifacts:
  files:
    - '**/*'
```

---

## **4️⃣ Fully Updated `pipeline.yml`**
To fully integrate **Docker Hub** into **AWS CodePipeline**, update the pipeline configuration.

Run the following command to **update or create `pipeline.yml`**:

```bash
cat <<EOF > pipeline.yml
AWSTemplateFormatVersion: '2010-09-09'
Resources:
  MyPipeline:
    Type: AWS::CodePipeline::Pipeline
    Properties:
      Name: DockerCI-CD-Pipeline
      RoleArn: arn:aws:iam::123456789012:role/CodePipelineRole
      ArtifactStore:
        Type: S3
        Location: my-codepipeline-artifacts-bucket
      Stages:
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
EOF
```

---

## **5️⃣ Explanation of What Was Added**
This update **fully integrates Docker Hub into AWS CodePipeline**, ensuring images are pushed after a successful build.

### **🔹 Key Enhancements**
✅ **Docker Image Push (`post_build` phase)**  
- **Pushes the built image** to Docker Hub **after the build** is successful.  
- **Ensures images are updated and available** for deployments.

✅ **Updated CodePipeline Configuration**
- **Ensures Docker Hub push automation**.
- **Maintains a fully automated CI/CD pipeline**.

---

## **6️⃣ Next Steps**
1️⃣ **Run the `sed` command** to update `buildspec.yml`.  
2️⃣ **Verify with `cat buildspec.yml`**.  
3️⃣ **Generate the new `pipeline.yml` file**.  
4️⃣ **Commit and push the changes**:

```bash
git add buildspec.yml pipeline.yml
git commit -m "Automated Docker Hub push integration in CodePipeline"
git push origin main
```

Now, your AWS **CodePipeline will automatically build and push images to Docker Hub**! 🚀
