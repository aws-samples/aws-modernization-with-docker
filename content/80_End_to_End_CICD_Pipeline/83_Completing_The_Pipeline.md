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

# Make sure the src/components/home directory exists
mkdir -p src/components/home

# Create a personalized change to the Home component with vibrant styling
cat <<EOF > src/components/home/Home.js
import React, { useState, useEffect } from 'react'
import { Link } from "react-router-dom";
import BannerImage from "../../images/choosinghouse.svg";
import "../home/home.css"

const Home = () => {
  const [colorIndex, setColorIndex] = useState(0);
  const colors = ['#FF6B6B', '#4ECDC4', '#FFD166', '#06D6A0', '#118AB2'];
  
  // Change color every 3 seconds for a dynamic effect
  useEffect(() => {
    const interval = setInterval(() => {
      setColorIndex((prevIndex) => (prevIndex + 1) % colors.length);
    }, 3000);
    return () => clearInterval(interval);
  }, []);

  return (
    <div className="home" style={{ backgroundImage: \`url(\${BannerImage})\` }}>
      <div className="headerContainer">
        <h1 className="animated-title" style={{ color: colors[colorIndex] }}> 
          ✨ Rent A Room ✨ 
        </h1>
        <p className="animated-subtitle"> 
          Travel Like A Nomad, <span className="highlight">Live Like A Local</span>
        </p>
        <div className="tagline-container">
          <p className="tagline">Your adventure begins with a single click</p>
        </div>
        <Link to="/rooms">
          <button className="glow-button"> Discover Your Next Home </button>
        </Link>
        <br></br>
        <div className="workshop-banner">
          <div className="ribbon"><span>CI/CD Success!</span></div>
          <h3>🎉 Workshop Completion 🎉</h3>
          <p className="deployer-info">This application was successfully deployed by <span className="deployer-name">${YOUR_NAME}</span> on <span className="deploy-date">$(date +"%B %d, %Y")</span></p>
          <p className="pipeline-info">Using AWS & Docker CI/CD Pipeline with:</p>
          <ul className="feature-list">
            <li><span className="feature-icon">🏗️</span> Multi-architecture builds with Docker Build Cloud</li>
            <li><span className="feature-icon">🔒</span> Security scanning with Docker Scout</li>
            <li><span className="feature-icon">🚀</span> Automated deployment to Amazon ECS</li>
            <li><span className="feature-icon">🌟</span> Seamless AWS & Docker integration</li>
          </ul>
          <div className="comparison-note">
            <p>Compare this version with your local development version to see the power of CI/CD!</p>
          </div>
        </div>
      </div>
    </div>
  )
}

export default Home
EOF

# Add enhanced CSS for the workshop banner and new UI elements
cat <<EOF >> src/components/home/home.css
/* Enhanced styling for the main components */
.headerContainer h1 {
  font-size: 3.5rem;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
  transition: color 1s ease;
}

.animated-title {
  animation: pulse 2s infinite;
}

.animated-subtitle {
  font-size: 1.8rem;
  margin-bottom: 1.5rem;
  color: #ffffff;
  text-shadow: 1px 1px 3px rgba(0, 0, 0, 0.5);
}

.highlight {
  background: linear-gradient(120deg, #84fab0 0%, #8fd3f4 100%);
  background-clip: text;
  -webkit-background-clip: text;
  color: transparent;
  font-weight: bold;
}

.tagline-container {
  margin: 15px 0;
}

.tagline {
  font-style: italic;
  color: #f8f9fa;
  font-size: 1.2rem;
}

.glow-button {
  padding: 12px 24px;
  font-size: 1.2rem;
  background: linear-gradient(45deg, #FF512F 0%, #F09819 100%);
  border: none;
  border-radius: 30px;
  color: white;
  font-weight: bold;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 0 15px rgba(255, 81, 47, 0.5);
}

.glow-button:hover {
  transform: translateY(-3px);
  box-shadow: 0 0 25px rgba(255, 81, 47, 0.8);
}

/* Workshop completion banner with enhanced styling */
.workshop-banner {
  margin-top: 40px;
  padding: 25px;
  background: linear-gradient(135deg, rgba(255, 255, 255, 0.9) 0%, rgba(240, 240, 250, 0.9) 100%);
  border-radius: 12px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
  max-width: 650px;
  margin-left: auto;
  margin-right: auto;
  position: relative;
  border-left: 5px solid #4ECDC4;
  overflow: hidden;
}

.ribbon {
  position: absolute;
  right: -5px;
  top: -5px;
  z-index: 1;
  overflow: hidden;
  width: 120px;
  height: 120px;
  text-align: right;
}

.ribbon span {
  font-size: 0.8rem;
  font-weight: bold;
  color: #FFF;
  text-transform: uppercase;
  text-align: center;
  line-height: 30px;
  transform: rotate(45deg);
  width: 150px;
  display: block;
  background: linear-gradient(#FF512F 0%, #F09819 100%);
  box-shadow: 0 3px 10px -5px rgba(0, 0, 0, 1);
  position: absolute;
  top: 30px;
  right: -35px;
}

.workshop-banner h3 {
  color: #2c3e50;
  margin-bottom: 15px;
  font-size: 1.8rem;
  text-align: center;
}

.deployer-info {
  color: #34495e;
  font-size: 1.1rem;
  margin-bottom: 15px;
  text-align: center;
}

.deployer-name {
  font-weight: bold;
  color: #118AB2;
  background-color: rgba(17, 138, 178, 0.1);
  padding: 2px 8px;
  border-radius: 4px;
}

.deploy-date {
  font-weight: bold;
  color: #06D6A0;
}

.pipeline-info {
  color: #34495e;
  font-size: 1.1rem;
  margin-bottom: 15px;
  text-align: center;
  font-weight: bold;
}

.feature-list {
  text-align: left;
  color: #34495e;
  padding-left: 20px;
  margin-bottom: 20px;
}

.feature-list li {
  margin-bottom: 10px;
  font-size: 1.05rem;
  list-style-type: none;
  padding-left: 10px;
  border-left: 3px solid transparent;
  transition: all 0.3s ease;
}

.feature-list li:hover {
  border-left-color: #4ECDC4;
  transform: translateX(5px);
}

.feature-icon {
  margin-right: 10px;
  font-size: 1.2rem;
}

.comparison-note {
  background-color: rgba(255, 209, 102, 0.2);
  padding: 10px 15px;
  border-radius: 8px;
  text-align: center;
  margin-top: 15px;
  border-left: 3px solid #FFD166;
}

.comparison-note p {
  color: #2c3e50;
  font-size: 0.95rem;
  margin: 0;
}

@keyframes pulse {
  0% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.05);
  }
  100% {
    transform: scale(1);
  }
}
EOF

# Make sure the images directory exists and create a placeholder if needed
mkdir -p src/images
if [ ! -f src/images/choosinghouse.svg ]; then
  echo "Creating placeholder image file..."
  cat <<EOF > src/images/choosinghouse.svg
<svg xmlns="http://www.w3.org/2000/svg" width="200" height="200" viewBox="0 0 200 200">
  <rect width="200" height="200" fill="#f0f0f0"/>
  <text x="50%" y="50%" font-family="Arial" font-size="16" text-anchor="middle">Rent-A-Room</text>
</svg>
EOF
fi

# Add the files to git and commit
git add src/components/home/Home.js src/components/home/home.css src/images/choosinghouse.svg
git commit -m "Add personalized workshop completion banner by ${YOUR_NAME}"
git push origin main
```

### 2️⃣ Monitor Pipeline Execution

1. Navigate to the AWS CodePipeline console using the URL from the CloudFormation outputs:

   ```
   https://console.aws.amazon.com/codepipeline/home?region=us-east-1#/view/docker-workshop-pipeline-Pipeline-*
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
echo "Retrieving ECS service details..."
aws ecs describe-services --cluster rent-a-room-cluster --services rent-a-room-service

# Get the load balancer URL with improved error handling
echo "Retrieving application URL..."
LB_URL=$(aws ecs describe-services --cluster rent-a-room-cluster --services rent-a-room-service \
  --query 'services[0].loadBalancers[0].targetGroupArn' --output text | \
  xargs -I {} aws elbv2 describe-target-groups --target-group-arns {} \
  --query 'TargetGroups[0].LoadBalancerArns[0]' --output text | \
  xargs -I {} aws elbv2 describe-load-balancers --load-balancer-arns {} \
  --query 'LoadBalancers[0].DNSName' --output text)

if [ -n "$LB_URL" ] && [ "$LB_URL" != "None" ]; then
  echo "✅ Application URL: http://$LB_URL"
  echo "Your application should be available in a few minutes as the ECS tasks start up."
else
  echo "⚠️ Could not retrieve application URL. Check the ECS service in the AWS Console."
fi
```

Visit the application URL to see your deployed application with the personalized message!

## 🔄 The Power of Integration: AWS and Docker

This pipeline demonstrates the seamless integration between AWS and Docker technologies:

| AWS Service            | Docker Technology      | Integration Value                      |
| ---------------------- | ---------------------- | -------------------------------------- |
| **AWS CodePipeline**   | **Docker Build Cloud** | Automated multi-architecture builds    |
| **AWS CodeBuild**      | **Docker Scout**       | Integrated security scanning           |
| **Amazon ECS**         | **Docker Images**      | Simplified container deployment        |
| **AWS CloudFormation** | **Docker Compose**     | Infrastructure and application as code |

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
# Delete the CloudFormation stack with confirmation
echo "Cleaning up resources..."
read -p "Are you sure you want to delete the CloudFormation stack? (y/n): " CONFIRM
if [[ $CONFIRM == "y" || $CONFIRM == "Y" ]]; then
  echo "Deleting CloudFormation stack..."
  aws cloudformation delete-stack --stack-name docker-workshop-pipeline

  echo "Waiting for stack deletion to complete (this may take several minutes)..."
  aws cloudformation wait stack-delete-complete --stack-name docker-workshop-pipeline

  if [ $? -eq 0 ]; then
    echo "✅ Stack deletion completed successfully"
  else
    echo "⚠️ Stack deletion may have encountered issues. Check the AWS Console."
  fi

  # Clean up the Docker Hub credentials secret
  echo "Deleting Docker Hub credentials from Secrets Manager..."
  aws secretsmanager delete-secret --secret-id dockerhub-credentials --force-delete-without-recovery || echo "⚠️ Secret deletion failed or secret doesn't exist"
else
  echo "Stack deletion cancelled"
fi
```

Thank you for participating in the AWS and Docker: Better Together workshop!
