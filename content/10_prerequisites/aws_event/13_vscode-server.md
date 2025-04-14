---
title: "VS Code Server Setup"
chapter: true
weight: 13
---

# Setting Up VS Code Server Access

In this section, you'll learn how to access the cloud-based VS Code environment that we'll use throughout the workshop.

## Prerequisites

- A modern web browser (Chrome, Firefox, or Edge recommended)
- Workshop credentials provided by your instructor

## Accessing the VS Code Server

### Step 1: Locate Your Credentials

1. Navigate to the **Event Outputs** section in the workshop portal
2. Look for two important pieces of information:
   - **VSCODEURL**: Your unique VS Code server URL
   - **Password**: Your secure access credentials

![Event Outputs Location](/images/Vscode-server-creds.png)

### Step 2: Launch VS Code Server

1. Click the **VSCODEURL** link in Event Outputs
2. A new browser tab will open with the VS Code login screen
3. Copy the password from Event Outputs
4. Paste it into the password field
5. Click **Login**

![VS Code Login Screen](/images/Vscode-server-login.png)

### Step 3: Verify Your Connection

Once logged in, you should see the VS Code interface:
- A file explorer on the left
- An editor panel in the center
- A terminal at the bottom (if not visible, press `` Ctrl + ` `` to open it)

![VS Code Interface](/images/Vscode-server-interface.png)

## Important Notes

- The VS Code server is secured through CloudFront for enhanced security
- Your session will remain active throughout the workshop
- All necessary extensions are pre-installed
- Work is automatically saved but will be lost when the workshop ends

## Troubleshooting

If you experience issues:
1. Clear your browser cache
2. Try using an incognito/private window
3. Ensure your corporate firewall isn't blocking the connection
4. Contact the workshop support team if problems persist

## Next Steps

✅ Verify you can access the VS Code server  
✅ Test the terminal functionality  
✅ Proceed to "Create a Docker Account" (skip if you already have one)  
✅ Continue to the CI/CD overview section

## Additional Resources

- [VS Code Documentation](https://code.visualstudio.com/docs)
- [VS Code Server FAQ](https://code.visualstudio.com/docs/remote/faq)