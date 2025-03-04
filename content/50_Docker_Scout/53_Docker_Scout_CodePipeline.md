---
title: "Automating Docker Scout in AWS CodePipeline"
chapter: false
weight:  53
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

## **3️⃣ Explanation of What Was Added**
This **security policy** ensures every build is **scanned for vulnerabilities** and fails if any critical ones are found.

```yaml
security_scan:
  commands:
    - echo Running Docker Scout Security Scan...
    - docker scout cves $DOCKER_USERNAME/myapp:latest --format json > scout-report.json
    - CRITICAL_COUNT=$(jq '[.vulnerabilities[] | select(.severity=="critical")] | length' scout-report.json)
    - if [ "$CRITICAL_COUNT" -gt 0 ]; then
        echo "❌ CRITICAL vulnerabilities found! Failing the build.";
        jq -r '".vulnerabilities[] | select(.severity==\"critical\") | \"CVE: \" + .id + \" | Package: \" + .package.name + \" | Suggested Fix: \" + (.fixes[].versions | join(\", \"))"' scout-report.json;
        exit 1;
      fi
```

---

## **4️⃣ Next Steps**
1️⃣ **Run the `sed` command** to update `buildspec.yml`.  
2️⃣ **Verify with `cat buildspec.yml`**.  
3️⃣ **Commit and push the changes**:

```bash
git add buildspec.yml
git commit -m "Added Docker Scout security scanning to CodePipeline"
git push origin main
```

Now, your AWS **CodePipeline will fail if critical vulnerabilities are found and provide suggested fixes**! 🚀
