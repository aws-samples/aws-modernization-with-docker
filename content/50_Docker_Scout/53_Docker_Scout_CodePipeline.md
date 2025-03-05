---
title: "Automating Docker Scout in AWS CodePipeline"
chapter: false
weight: 53
---

# 🔄 Automating Docker Scout in AWS CodePipeline

Now, let’s **integrate Docker Scout** into **AWS CodePipeline** to automate security scanning before pushing images.

---

## **1️⃣ Update `buildspec.yml` to Add Docker Scout**
We will **append** a new phase to **run a security scan** during the build.

Run the following command to **add the scan step** before the `post_build` phase:

```bash
sed -i '/post_build:/i\
  security_scan:\
    commands:\
      - echo Running Docker Scout Security Scan...\
      - docker scout cves $DOCKER_USERNAME/myapp:latest --format json > scout-report.json\
      - CRITICAL_COUNT=$(jq '[.vulnerabilities[] | select(.severity=="critical")] | length' scout-report.json)\
      - if [ "$CRITICAL_COUNT" -gt 0 ]; then\
          echo "❌ CRITICAL vulnerabilities found! Failing the build.";\
          jq -r '".vulnerabilities[] | select(.severity==\"critical\") | \"CVE: \" + .id + \" | Package: \" + .package.name + \" | Suggested Fix: \" + (.fixes[].versions | join(\", \"))"' scout-report.json;\
          exit 1;\
        fi\
' buildspec.yml
```

---

## **2️⃣ Verify the Changes**
Check the updated `buildspec.yml`:

```bash
cat buildspec.yml
```

---

## **3️⃣ Fully Updated `buildspec.yml`**
Here is what your **final `buildspec.yml`** should look like **after** adding Docker Scout:

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

  security_scan:
    commands:
      - echo Running Docker Scout Security Scan...
      - docker scout cves $DOCKER_USERNAME/myapp:latest --format json > scout-report.json
      - CRITICAL_COUNT=$(jq '[.vulnerabilities[] | select(.severity=="critical")] | length' scout-report.json)
      - if [ "$CRITICAL_COUNT" -gt 0 ]; then
          echo "❌ CRITICAL vulnerabilities found! Failing the build.";
          jq -r '".vulnerabilities[] | select(.severity=="critical") | "CVE: " + .id + " | Package: " + .package.name + " | Suggested Fix: " + (.fixes[].versions | join(", "))"' scout-report.json;
          exit 1;
        fi

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
To fully integrate **Docker Scout** into **AWS CodePipeline**, update the pipeline configuration.

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
**Note: Replace `REPLACE_WITH_YOUR_GITHUB_USERNAME` and `REPLACE_WITH_YOUR_GITHUB_REPO` with your repsective values.**

---

## **5️⃣ Explanation of What Was Added**
This **security policy** ensures every build is **scanned for vulnerabilities** and fails if any critical ones are found.

### **🔹 Key Enhancements**
✅ **Docker Scout Scan (`security_scan` phase)**
- **Extracts vulnerabilities** from the Docker Scout scan report.
- **Fails the pipeline** if critical vulnerabilities exist.
- **Provides suggested fixes** from the report.

✅ **Updated CodePipeline Configuration**
- **Ensures Docker Scout runs before pushing the image.**
- **Maintains a fully automated security-first CI/CD pipeline.**

---

## **6️⃣ Next Steps**
1️⃣ **Run the `sed` command** to update `buildspec.yml`.  
2️⃣ **Verify with `cat buildspec.yml`**.  
3️⃣ **Generate the new `pipeline.yml` file**.  
4️⃣ **Commit and push the changes**:

```bash
git add buildspec.yml pipeline.yml
git commit -m "Added Docker Scout security scanning to CodePipeline"
git push origin main
```

Now, your AWS **CodePipeline will fail if critical vulnerabilities are found and provide suggested fixes**! 🚀
