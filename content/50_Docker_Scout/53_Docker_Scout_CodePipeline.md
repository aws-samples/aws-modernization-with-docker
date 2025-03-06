---
title: "Automating Docker Scout in AWS CodePipeline"
chapter: false
weight: 53
---

# 🔄 Automating Docker Scout in AWS CodePipeline

Now, let's **integrate Docker Scout** into our pipeline to automate security scanning before pushing images.

---

## **1️⃣ Update `buildspec.yml` to Add Docker Scout**
We will **append** a new phase to **run a security scan** during the build, which will analyze our images for vulnerabilities before pushing to Docker Hub.

Run the following command to **add the scan step** before the `post_build` phase:

```bash
sed -i '/post_build:/i\
  security_scan:\
    commands:\
      - echo Running Docker Scout Security Scan...\
      - docker scout cves $DOCKER_USERNAME/rent-a-room:latest --format json > scout-report.json\
      - CRITICAL_COUNT=$(jq '\''[.vulnerabilities[] | select(.severity=="critical")] | length'\'' scout-report.json)\
      - if [ "$CRITICAL_COUNT" -gt 0 ]; then\
          echo "❌ CRITICAL vulnerabilities found! Failing the build.";\
          jq -r '\''.vulnerabilities[] | select(.severity=="critical") | "CVE: " + .id + " | Package: " + .package.name + " | Suggested Fix: " + (.fixes[].versions | join(", "))'\'' scout-report.json;\
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
      - docker buildx build --platform linux/amd64,linux/arm64 -t $DOCKER_USERNAME/rent-a-room:latest --load .

  security_scan:
    commands:
      - echo Running Docker Scout Security Scan...
      - docker scout cves $DOCKER_USERNAME/rent-a-room:latest --format json > scout-report.json
      - CRITICAL_COUNT=$(jq '[.vulnerabilities[] | select(.severity=="critical")] | length' scout-report.json)
      - if [ "$CRITICAL_COUNT" -gt 0 ]; then
          echo "❌ CRITICAL vulnerabilities found! Failing the build.";
          jq -r '.vulnerabilities[] | select(.severity=="critical") | "CVE: " + .id + " | Package: " + .package.name + " | Suggested Fix: " + (.fixes[].versions | join(", "))' scout-report.json;
          exit 1;
        fi

  post_build:
    commands:
      - echo Build completed on `date`
      - echo Pushing the Docker image to Docker Hub...
      - docker push $DOCKER_USERNAME/rent-a-room:latest

artifacts:
  files:
    - '**/*'
```

The key features of our updated `buildspec.yml` are:

1. **Pre-build phase**: Login to Docker Hub and set up BuildKit
2. **Build phase**: Build multi-architecture Docker images
3. **Security scan phase**: Run Docker Scout to analyze vulnerabilities and fail if critical issues are found
4. **Post-build phase**: Push the image to Docker Hub only if security checks pass

---

## **4️⃣ Understanding the Security Pipeline**

The **security_scan** phase works as follows:

1. Runs `docker scout cves` on our image and saves the results as JSON
2. Counts the number of critical vulnerabilities using `jq`
3. If any critical vulnerabilities are found:
   - Displays a failure message
   - Lists all critical vulnerabilities with their IDs, affected packages, and suggested fixes
   - Fails the build with exit code 1, preventing the image from being pushed

This creates a security gate ensuring only images that pass the vulnerability check make it to Docker Hub.

---

## **5️⃣ No Changes to Pipeline Configuration**

Our pipeline configuration from the previous section already supports this workflow, as the CodeBuild project will automatically process our updated buildspec.yml. The full pipeline will continue with the same configuration:

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

---

## **6️⃣ Benefits of This Security-Enhanced Pipeline**

✅ **Automated vulnerability scanning** before images are pushed  
✅ **Fail-fast approach** to prevent vulnerable images from reaching production  
✅ **Detailed vulnerability reports** to help developers fix security issues  
✅ **Simple implementation** using existing Docker Scout capabilities  
✅ **Security-first posture** with minimal pipeline configuration changes

---

## **7️⃣ Next Steps**
1️⃣ **Run the `sed` command** to update `buildspec.yml`.  
2️⃣ **Verify with `cat buildspec.yml`**.  
3️⃣ **Commit and push the changes**:

```bash
git add buildspec.yml
git commit -m "Added Docker Scout security scanning to CodePipeline"
git push origin main
```

Now, your AWS **CodePipeline will automatically scan for vulnerabilities and fail if critical issues are found** before pushing images to Docker Hub! 🚀
