---
title: "Scanning Docker Images with Docker Scout"
chapter: false
weight: 51
---

# 🔍 Scanning Docker Images with Docker Scout

Now that we understand what **Docker Scout** is, let’s **scan our existing Docker image** for security vulnerabilities.

---

## **1️⃣ Install Docker Scout**
Ensure Docker Scout is installed by running:

```bash
docker scout version
```

If it’s not installed, update Docker to the latest version or manually install it:

```bash
curl -fsSL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh -o install-scout.sh
sh install-scout.sh
```

---

## **2️⃣ Scan Our Application Image**
We will scan the **Docker image we built and pushed** in the previous section:

```bash
docker scout quickview $DOCKER_USERNAME/rent-a-room:latest
```
![Summary](/images/Summaryscout.png)

🔹 This will display a **summary of vulnerabilities** in the image.  
🔹 To get a **detailed report**, run:

```bash
docker scout cves $DOCKER_USERNAME/rent-a-room:latest
```

**Example Output:**

![Scout](/images/Detailedscout.png)

🔹 Each outcome is a link to Docker Scout, click to see more 


---

## **3️⃣ Understanding the Output**
| Column | Description |
|--------|-------------|
| **CVE ID** | The identifier of the vulnerability. |
| **Severity** | The risk level (Low, Medium, High, Critical). |
| **Affected Package** | The component that contains the vulnerability. |
| **Remediation** | Suggested upgrade or patch. |

---

## **📌 Next Steps**
Now that we’ve **identified vulnerabilities**, let's **remediate them** in our Dockerfile.  
Move to the **next section** to learn how to fix security issues. 🚀
