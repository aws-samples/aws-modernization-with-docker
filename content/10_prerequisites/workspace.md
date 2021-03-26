---
title: "Create a workspace"
chapter: false
weight: 12
---

{{% notice warning %}}
The Cloud9 workspace should be built by an IAM user with Administrator privileges,
not the root account user. Please ensure you are logged in as an IAM user, not the root
account user.
{{% /notice %}}

{{% notice info %}}
This workshop was designed to run in the **N.Virginia (us-east-1)** region. **Please don't
run in any other region.** Future versions of this workshop will expand region availability,
and this message will be removed.
{{% /notice %}}

{{% notice tip %}}
Ad blockers, javascript disablers, and tracking blockers should be disabled for
the cloud9 domain, or connecting to the workspace might be impacted.
Cloud9 requires third-party-cookies. You can whitelist the [specific domains]( https://docs.aws.amazon.com/cloud9/latest/user-guide/troubleshooting.html#troubleshooting-env-loading).
{{% /notice %}}

### Launch Cloud9:
Create a Cloud9 Environment: [https://us-east-1.console.aws.amazon.com/cloud9/home?region=us-east-1](https://us-east-1.console.aws.amazon.com/cloud9/home?region=us-east-1)

{{% notice warning %}}
Make sure you are naming your Cloud9 environment `Docker-Workshop`, otherwise things will break later.
{{% /notice %}}

1. Select **Create environment**
2. Name it **Docker-Workshop** and click **Next Step**
3. Use the following table for the configuration:

    |    Environment Setting   |   Value    |
    |----------|--------------------|
    | Envrionment Type | Ubuntu Server 18.04 LTS |
    | Instance Type | t3.large |
    | Platform | (Leave as default)|
    | Cost-Saving settings | (Leave as default)|
    | IAM Role | (Leave as default) |
4. Click **Next Step** and then  **Create Environment**
5.  When it comes up, customize the environment by closing the **welcome tab**
and **lower work area**, and opening a new **terminal** tab in the main work area. Your workspace should now look like this:
![create-workspace](/images/create-workspace.png)

- If you like this theme, you can choose it yourself by selecting **View / Themes / Solarized / Solarized Dark**
in the Cloud9 workspace menu.
