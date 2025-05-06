---
title: "Create Docker Hub Account"
chapter: true
weight: 32
---

# 🔑 Create a Docker Hub Account

In this section, we will:

- ✅ **Create a Docker Hub account** (if you don't have one already)
- ✅ **Generate a Docker Hub access token** for secure CLI authentication

---

## **1️⃣ Sign Up for Docker Hub**

If you don't already have a Docker Hub account, follow these steps:

1. Visit [Docker Hub](https://hub.docker.com/) in your browser
2. Click **Sign Up** in the top-right corner
3. Enter your information:
   - Docker ID (username)
   - Email address
   - Password
4. Complete the verification process
5. Log in to your new account

![Docker Hub Sign Up](/images/dockerhub-signup.png)

---

## **2️⃣ Create an Access Token**

For secure CLI authentication, create a personal access token:

1. Navigate to [Personal Access Tokens](https://app.docker.com/settings/personal-access-tokens) section in your Docker Hub.
2. Click on **Generate new token**
3. Enter a description (e.g., "Workshop Token")
4. Choose **30 days** for the Expiration date.
5. Select appropriate permissions as **Read, Write, Delete**
6. Click **Generate**
7. **IMPORTANT**: Copy and save your token immediately! It will only be shown once. Or just keep the browser tab open.

![Docker Hub Access Token](/images/token-creation.png)

### **Preparing for CLI Authentication**

After generating your token:

1. **Copy your Docker Hub username** from the top-right corner of the page
2. **Copy the generated token** from the dialog box (you'll need both in the next section)
3. Keep these values handy - you'll need to enter them when prompted in the next step

![Docker CLI Login Information](/images/token-settings.png)

> **Note**: Don't close this browser tab until you've completed the next section. You won't be able to see the token again after closing the dialog.

---

## **3️⃣ Token Best Practices**

Follow these security best practices for your access token:

- ✅ **Store securely**: Keep your token in a password manager
- ✅ **Set expiration**: Consider setting an expiration date
- ✅ **Limit scope**: Only grant necessary permissions
- ✅ **Rotate regularly**: Create new tokens periodically
- ✅ **Never share**: Keep tokens confidential
- ✅ **Revoke if compromised**: Delete tokens if security is breached

---

## **4️⃣ Next Steps**

Now that you have a Docker Hub account and access token:

1. **Keep your token handy** for the next section
2. **Proceed to Docker Hub CLI Authentication** to connect your local environment
3. **Prepare to push and pull images** from Docker Hub
