+++
title = "Module 4"
chapter = true
weight = 23
+++

# CI/CD with GitHub Actions and Docker Compose

**Estimated Completion Time: 1 Hour**

![Docker](/images/githubactions.png)

In this module, we will cover how to automate our workflow by adding a CI/CD pipeline using GitHub Actions to our Docker Compose application. For organizations using GitHub as a source code repository, GitHub Actions provide a way to implement complex CI/CD functionality directly in GitHub by initiating a workflow on any GitHub event. One of the benefits of using GitHub Actions as your CI/CD pipeline of choice is that it includes a variety of plugins to help build our workflow pipelines geared towards our applications. For our application, we will build a Github Actions workflow that will automate the deployment process of building and pushing our Compose application to Docker Hub which will then automate deploying our application to ECS. 

## What is GitHub Actions?
A GitHub Action is an individual unit of functionality that can be combined with other GitHub Actions to create workflows, which are triggered in response to certain GitHub events, for example, pull, push, or commit events.  Workflows run inside managed environments on GitHub-hosted servers so it saves you the operational overhead and complexity of having to manage these servers yourself. You define your workflows in YAML so you can easily define the specific event to kick off your workflow and customize further to meet your application needs. 

GitHub Actions uses Docker under the hood and exposes that to end users. The benefit of this is that all the standard Docker commands you would use on a daily basis work out of the box without you having to add additional configurations. So all the different Docker tools and services that we have been using throughout this workshop can be added to your workflow in a simple YAML configuration file. 

## GitHub Actions Marketplace 
While we will only be exploring how to create a GitHub Actions workflow for our Compose application, we encourage you to browse the GitHub Actions marketplace to learn about the different workflows you can build for your applications needs. Go ahead and visit https://github.com/marketplace for more information!

## CI/CD Overview
One of the most important things to understand before building out your GitHub Actions workflows is to understand the difference between CI (continuous integration) and CD (continuous deployment) as this concept can be confusing for users that are not familar with CI/CD concepts. Let's go ahead and break down the differences so that the rest of this module will make more sense. 

### Continuous Integration (CI) 
Continuous Integration (CI) is the concept of continuously integrating your code changes to a central repository where automated builds and tests run to ensure that there are no integration challenges that can happen right before those changes are merged into the release branch. We have all heard nightmare stories about code changes that include bugs which cause delays in feature/product releases so it is crucial that you carefully define what your test and build process to have an efficient CI system in place. 

### Continuous Development (CD) and Continuous Deployment (CD)

Continuous Development (CD) is the process of automatically deploying all code changes to a testing and/or production environment after the build stage of your workflow is complete. This means that on top of automated testing, you have an automated release process and you can deploy your application any time by clicking a button. You have the option to decide how often you want to deploy your code but we recommend that you deploy your code to production in small batches as early as possible in cases where you need to troubleshoot. 

CD can also be known as continuous deployment which is different than continuous development as defined above. Continuous deployment actually goes a step further than continuous development in that every change that passes all stages of your production pipeline is released to your end users or customers. There is no human intervention and only a failed test will prevent a new change to be deployed to production. The benefit here is that you accelerate deployment times and your developer teams can focus on building software as they can see their work go live instantenously. 

### CI/CD
Now that we understand the difference between CI and CD, let's discuss why it is important to utilize both in your deployment pipelines and workflows especially while using GitHub Actions. In reality, CI and CD are very much intertwined and depend on each other in order to have an effective CI/CD pipeline or workflow. With GitHub Actions as your CI/CD pipeline/workflow of choice, you get the following benefits:
    
 1. GitHub hosts the server where your CI tests would ususally run so you don't need to worry about managing a server for your CI
 2. GitHub Actions supports logical operations so for example you could push only on tags instead on every commit. 
 3. You can run a workflow on any GitHub event so for example if an issue is created, pull request has been opened, or even when a tag has been put on a repository. 
 4. Define your workflows in YAML which can be stored in your repository at .github.workflows 
 5. The GitHub Actions marketplace provides a variety of different options you can choose for both your CI and CD needs

## Summary
In this section, we have defined the differences between CI and CD, what GitHub Actions are, and why it is important to utilize the concept of CI/CD in your GitHub Actions workflow. In the next section, we will walk you through how to set up your GitHub Actions workflow for our Docker Compose application. 