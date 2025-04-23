---
title: "Docker Hub CLI Authentication"
chapter: true
weight: 42
---

# 🔐 Docker Hub CLI Authentication

In this section, we will:
- ✅ **Authenticate with Docker Hub** via CLI.
- ✅ **Securely store credentials in AWS Secrets Manager** for use in **AWS CodePipeline**.

---

## **1️⃣ Docker Hub Login via CLI**

### **🔹 Prompting for Docker Credentials**
Run the following command to prompt the user for **Docker Hub credentials** and store them in variables:

```bash
echo '
# Docker Hub Credentials
export DOCKER_USERNAME="'$(read -p "Enter Docker Hub username: " username; echo $username)'"
export DOCKER_TOKEN="'$(read -s -p "Enter Docker Hub token: " token; echo $token)'"' >> ~/.bashrc

source ~/.bashrc
```

To push and pull images from Docker Hub, log in via CLI:

```bash
docker login -u $DOCKER_USERNAME -p $DOCKER_TOKEN
```

![Docker Login](/images/docker-cli-login.png)

---

## **2️⃣ Secure Credential Storage in AWS Secrets Manager**
Instead of hardcoding credentials, we will **store them securely** in AWS Secrets Manager.

Create a **secure AWS secret** using the entered credentials:

```bash
aws secretsmanager create-secret --name dockerhub-credentials \
  --description "Docker Hub credentials for AWS CodePipeline" \
  --secret-string "{\"DOCKER_USERNAME\":\"$DOCKER_USERNAME\", \"DOCKER_TOKEN\":\"$DOCKER_TOKEN\"}"
```

✅ This command securely **stores `DOCKER_USERNAME` and `DOCKER_TOKEN`** in AWS Secrets Manager.

---

## **3️⃣ Verify Secret Creation**
Run the following to confirm the secret exists:

```bash
aws secretsmanager list-secrets | grep dockerhub-credentials
```

To view the stored values (for debugging purposes only):

```bash
aws secretsmanager get-secret-value --secret-id dockerhub-credentials --query SecretString --output json
```

---

## **4️⃣ Next Steps**
Now that credentials are securely stored:
1. **AWS CodePipeline will retrieve them during builds.**  
2. **No need to manually enter Docker credentials again!**  
3. **Move to the next section** to automate Docker builds in AWS CodePipeline. 🚀
