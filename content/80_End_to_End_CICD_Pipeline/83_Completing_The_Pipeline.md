---
title: "Completing the Pipeline"
chapter: false
weight: 83
---

# 🏁 Completing the End-to-End CI/CD Pipeline

Congratulations! You've successfully set up the AWS CodePipeline infrastructure with CloudFormation. Now let's make it come alive by triggering the pipeline and watching the entire workflow in action.

## 🔄 Pipeline Overview

Our complete pipeline includes these stages:

1. **Source**: Pull code from GitHub repository
2. **Build**: Build multi-architecture Docker images with Docker Build Cloud
3. **Security Scan**: Analyze images with Docker Scout
4. **Deploy**: Deploy to Amazon ECS

![complete-pipeline](/images/complete-pipeline.png)

## 🚀 Triggering the Pipeline

Let's make a change to our application that will trigger the pipeline. We'll add a personalized welcome message that includes your name and today's date.

### 1️⃣ Create a Personalized Change

Let's modify the Home component to add a personalized workshop completion banner:

```bash
# Navigate to your existing Rent-A-Room repository that you forked earlier
cd Rent-A-Room

# Ask for your name to personalize the banner
echo "Enter your name for the workshop completion banner:"
read YOUR_NAME

# Create a personalized change to the Home component
cat <<EOF > src/components/home/Home.js
import React from 'react'
import { Link } from "react-router-dom";
import BannerImage from "../../images/choosinghouse.svg";
import "../home/home.css"

const Home = () => {
  return (
    <div className="home" style={{ backgroundImage: \`url(\${BannerImage})\` }}>
      <div className="headerContainer">
        <h1> Rent A Room </h1>
        <p> Travel Like A Nomad</p>
        <Link to="/rooms">
          <button> Rent Now </button>
        </Link>
        <br></br>
        <div className="workshop-banner">
          <h3>🎉 Workshop Completion 🎉</h3>
          <p>This application was successfully deployed by ${YOUR_NAME} on $(date +"%B %d, %Y")</p>
          <p>Using AWS & Docker CI/CD Pipeline with:</p>
          <ul>
            <li>Multi-architecture builds with Docker Build Cloud</li>
            <li>Security scanning with Docker Scout</li>
            <li>Automated deployment to Amazon ECS</li>
          </ul>
        </div>
      </div>
    </div>
  )
}

export default Home
EOF

# Add some CSS for the workshop banner
cat <<EOF >> src/components/home/home.css

/* Workshop completion banner */
.workshop-banner {
  margin-top: 30px;
  padding: 15px;
  background-color: rgba(255, 255, 255, 0.9);
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  max-width: 600px;
  margin-left: auto;
  margin-right: auto;
}

.workshop-banner h3 {
  color: #2c3e50;
  margin-bottom: 10px;
}

.workshop-banner p {
  color: #34495e;
  font-size: 16px;
  margin-bottom: 10px;
}

.workshop-banner ul {
  text-align: left;
  color: #34495e;
  padding-left: 20px;
}

.workshop-banner li {
  margin-bottom: 5px;
}
EOF

# Add the files to git and commit
git add src/components/home/Home.js src/components/home/home.css
git commit -m "Add personalized workshop completion banner by ${YOUR_NAME}"
git push origin main
```

### 2️⃣ Monitor Pipeline Execution

1. Navigate to the AWS CodePipeline console using the URL from the CloudFormation outputs:
   ```
   https://console.aws.amazon.com/codepipeline/home?region=us-east-1#/view/ECSPipelineStack-Pipeline-*
   ```

2. Watch as your pipeline progresses through each stage:
   - **Source**: Pulling your latest code with the personalized message
   - **Build**: Building the Docker image with Docker Build Cloud
   - **Security Scan**: Scanning the image with Docker Scout
   - **Deploy**: Deploying to Amazon ECS

![pipeline-execution](/images/pipeline-execution.png)

## 🔍 Examining Docker Scout Results

Once the pipeline reaches the Security Scan stage, let's examine the Docker Scout results:

1. Navigate to the Docker Scout dashboard: https://scout.docker.com/
2. Find your `rent-a-room` repository
3. Review the security scan results:
   - Vulnerability summary
   - Package details
   - Recommended fixes

![docker-scout-results](/images/docker-scout-results.png)

## 🚢 Verifying ECS Deployment

After the pipeline completes, verify your application is running on Amazon ECS:

```bash
# Get the ECS service details
aws ecs describe-services --cluster rent-a-room-cluster --services rent-a-room-service

# Get the load balancer URL (if applicable)
aws ecs describe-services --cluster rent-a-room-cluster --services rent-a-room-service \
  --query 'services[0].loadBalancers[0].targetGroupArn' --output text | \
  xargs -I {} aws elbv2 describe-target-groups --target-group-arns {} \
  --query 'TargetGroups[0].LoadBalancerArns[0]' --output text | \
  xargs -I {} aws elbv2 describe-load-balancers --load-balancer-arns {} \
  --query 'LoadBalancers[0].DNSName' --output text
```

Visit the application URL to see your deployed application with the personalized message!

## 🔄 The Power of Integration: AWS and Docker

This pipeline demonstrates the seamless integration between AWS and Docker technologies:

| AWS Service | Docker Technology | Integration Value |
|-------------|-------------------|------------------|
| **AWS CodePipeline** | **Docker Build Cloud** | Automated multi-architecture builds |
| **AWS CodeBuild** | **Docker Scout** | Integrated security scanning |
| **Amazon ECS** | **Docker Images** | Simplified container deployment |
| **AWS CloudFormation** | **Docker Compose** | Infrastructure and application as code |

## 💡 Key Takeaways

Through this workshop, you've experienced firsthand how AWS and Docker work better together:

1. **Accelerated Development**: From code to production in minutes, not days
2. **Enhanced Security**: Shift-left security with Docker Scout integrated into the pipeline
3. **Operational Excellence**: Fully automated, repeatable deployments
4. **Multi-Architecture Support**: Build once, deploy anywhere with Docker Build Cloud
5. **Cost Optimization**: Pay-as-you-go model for both AWS and Docker services

## 🚀 Next Steps

To further enhance your AWS and Docker integration:

1. **Implement Docker Scout Policy Gates**: Prevent vulnerable images from being deployed
2. **Add Automated Testing**: Include unit and integration tests in your pipeline
3. **Set Up Blue/Green Deployments**: Enable zero-downtime updates
4. **Implement Container Insights**: Monitor container performance with CloudWatch
5. **Explore AWS App Runner**: For even simpler container deployments

## 🎉 Congratulations!

You've successfully built a complete end-to-end CI/CD pipeline that leverages the best of AWS and Docker technologies. This modern approach to application development and deployment will help you deliver secure, scalable applications faster than ever before.

Remember to clean up your resources when you're done with the workshop to avoid unnecessary charges:

```bash
# Delete the CloudFormation stack
aws cloudformation delete-stack --stack-name ECSPipelineStack

# Wait for stack deletion to complete
aws cloudformation wait stack-delete-complete --stack-name ECSPipelineStack
```

Thank you for participating in the AWS and Docker: Better Together workshop!
