+++
title = "Module 3"
chapter = true
weight = 23
+++

# Introduction to CI/CD

**Estimated Completion Time: 1 Hour**

Continuous Integration/Continuous Delivery (CI/CD) can be pictured as a pipeline, where new code is submitted on one end, tested over a series of stages (source, build, test, staging, and production), and then published as production-ready code. 
![Docker](/images/cicdpipeline.png)


### CICD pipeline overview
Each stage of the CI/CD pipeline is structured as a logical unit in the delivery process. Each stage acts as a gate that vets a certain aspect of the code. As the code progresses through the pipeline, the assumption is that the quality of the code is higher in the later stages, because more aspects of it continue to be verified. Problems uncovered in an early stage stop the code from progressing through the pipeline. Results from the tests are immediately sent to the team, and all further builds and releases are stopped if software does not pass the stage.

AWS brings in a complete set of CI/CD developer tools to accelerate software development and release cycles. AWS CodePipeline automates the build, test, and deploy phases of the release process every time there is a code change, based on the defined release model. This enables the rapid and reliable delivery of features and updates.
AWS CodePipeline can address a variety of development and operation use cases including:

    - Compiling, building, and testing code with AWS CodeBuild.
    - Continuous delivery of container-based applications to the cloud.
    - Pre-deployment validation of artifacts (such as descriptors and container images) 
      required for network service or specific cloud-native network functions.
    - Functional, integration, and performance tests, including baseline and 
      regression testing.
    - Reliability and disaster recovery (DR) testing.


AWS CodePipeline can integrate with other services. These can be AWS services such as Amazon Simple Storage Service (Amazon S3), or third-party products such as GitHub, Docker BuildKit, Docker Scout and Docker Hub.

![Docker](/images/cicdpipeline2.png)


### AWS CI/CD pipeline components
AWS can set up CI/CD pipelines using the following AWS developer tools:

    - AWS CodeConnections (to use GitHub repository).
    - AWS CodeBuild.
    - AWS CodePipeline.
    - AWS CodeDeploy.
    - Amazon Elastic Container Registry.
    - Docker Build Cloud
    - Docker Scout
    - Docker TestContainers
    - Docker Hub

The creation of CI/CD pipelines using these services can be automated using AWS CDK and AWS CloudFormation.
 

### Continuous Integration 

Continuous Integration (CI) is a DevOps software development practice where developers regularly merge their code changes into a central repository, after which automated builds and tests are run. Continuous Integration most often refers to the build or integration stage of the software release process and entails both an automation component (e.g. a CI or build service) and a cultural component (e.g. learning to integrate frequently). The key goals of Continuous Integration are to find and address bugs quicker, improve software quality, and reduce the time it takes to validate and release new software updates.

### Why is Continuous Integration needed?

In the past, developers on a team might work in isolation for an extended period of time and only merge their changes to the mainline branch once their work was completed. This made merging code changes difficult and time-consuming, and also resulted in bugs accumulating for a long time without correction. These factors made it harder to deliver updates to customers quickly.


### How does Continuous Integration work?

With Continuous Integration, developers frequently commit to a shared repository using a version control system such as Git. Prior to each commit, developers may choose to run local unit tests on their code as an extra verification layer before integrating. A Continuous Integration service automatically builds and runs unit tests on the new code changes to immediately surface any errors.

![Docker](/images/cicdpipeline3.png)

Continuous Integration refers to the build and unit testing stages of the software release process. Every revision that is committed triggers an automated build and test. With continuous delivery, code changes are automatically built, tested, and prepared for a release to production. Continuous delivery expands upon Continuous Integration by deploying all code changes to a testing environment and/or a production environment after the build stage.

### Continuous Delivery Explained

Continuous Delivery (CD) is a software development practice where code changes are automatically prepared for a release to production. A pillar of modern application development, continuous delivery expands upon continuous integration by deploying all code changes to a testing environment and/or a production environment after the build stage. When properly implemented, developers will always have a deployment-ready build artifact that has passed through a standardized test process.

Continuous Delivery lets developers automate testing beyond just unit tests so they can verify application updates across multiple dimensions before deploying to customers. These tests may include UI testing, load testing, integration testing, API reliability testing, etc. This helps developers more thoroughly validate updates and preemptively discover issues. With the cloud, it is easy and cost-effective to automate the creation and replication of multiple environments for testing, which was previously difficult to do on-premises.


### Continuous Delivery vs. Continuous Deployment

With Continuous Delivery, every code change is built, tested, and then pushed to a non-production testing or staging environment. There can be multiple, parallel test stages before a production deployment. The difference between Continuous Delivery and Continuous Deployment is the presence of a manual approval to update to production. With Continuous Deployment, production happens automatically without explicit approval.

![Docker](/images/cicdpipeline4.png)

Continuous Deployment automates the entire software release process. Every revision that is committed triggers an automated flow that builds, tests, and then stages the update. The final decision to deploy to a live production environment is triggered by the developer.