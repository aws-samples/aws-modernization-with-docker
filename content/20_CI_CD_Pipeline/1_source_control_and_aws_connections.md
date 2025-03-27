+++
title = "Source Control and AWS CodeConnections"
chapter = true
weight = 21
+++


# Source Control and AWS CodeConnections


Source control (or version control) is the practice of tracking and managing changes to code. Source Control Management (SCM) systems provide a running history of code development and help to resolve conflicts when merging contributions from multiple sources.


### Source control basics
Whether you are writing a simple application on your own or collaborating on a large software development project as part of a team, source control is a vital component of the development process. Source code management systems allow you to track your code change, see a revision history for your code, and revert to previous versions of a project when needed. With source code management systems, you can collaborate on code with your team, isolate your work until it is ready, and quickly trouble-shoot issues by identifying who made changes and what the changes were. Source code management systems help streamline the development process and provide a centralized source for all your code.



### What is Git?
Git is an open-source distributed source code management system. Git allows you to create a copy of your repository known as a branch. Using this branch, you can then work on your code independently from the stable version of your codebase. Once you are ready with your changes, you can store them as a set of differences, known as a commit. You can pull in commits from other contributors to your repository, push your commits to others, and merge your commits back into the main version of the repository.

[Learn more about Git..](https://aws.amazon.com/devops/source-control/git/)



### AWS CodeConnections
AWS CodeConnections is a feature in the Developer Tools console to connect AWS resources such as AWS CodePipeline to external code repositories. Each connection is a resource that you can give to AWS services to connect to a third-party repository, such as Git or BitBucket. You can use a connection ARN in your stack templates for CodeBuild build projects, CodeDeploy applications, and pipelines in CodePipeline, without the need to reference stored secrets or parameters.

[Learn more about AWS CodeConnections..](https://docs.aws.amazon.com/dtconsole/latest/userguide/welcome-connections.html#welcome-connections-what-can-I-do)


