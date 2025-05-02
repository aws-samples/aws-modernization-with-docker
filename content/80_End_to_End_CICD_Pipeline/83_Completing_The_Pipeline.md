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

## 🚀 Triggering the Pipeline with a Fun Customization

Let's make a meaningful change to our application that will trigger the pipeline. We'll customize the Rent-A-Room application with a personalized workshop completion banner and enhanced UI elements.

### 1️⃣ Create a Personalized Change

Let's modify the Home component to add a personalized workshop completion banner:

```bash
# Navigate to your existing Rent-A-Room repository that you forked earlier
cd /workshop/Rent-A-Room

# Upgrade Home component
cat <<EOF > src/components/home/Home.js
import React, { useState, useEffect } from 'react';
import { Link } from "react-router-dom";
import BannerImage from "../../images/choosinghouse.svg";
import "./home.css";

const Home = () => {
  return (
    <div className="home-container">
      <div className="hero-section">
        <div className="hero-content">
          <h1 className="hero-title">Find Your Perfect Stay</h1>
          <p className="hero-subtitle">
            Discover unique spaces that feel just like home
          </p>
          <Link to="/rooms">
            <button className="cta-button">
              Explore Rooms
              <span className="arrow">→</span>
            </button>
          </Link>
        </div>
        <div className="hero-image">
          <img src={BannerImage} alt="Room illustration" />
        </div>
      </div>

      <div className="features-section">
        <div className="feature-card">
          <div className="feature-icon">🏠</div>
          <h3>Verified Homes</h3>
          <p>All our properties are carefully vetted for quality</p>
        </div>
        <div className="feature-card">
          <div className="feature-icon">💫</div>
          <h3>Best Prices</h3>
          <p>Find competitive rates for both short and long stays</p>
        </div>
        <div className="feature-card">
          <div className="feature-icon">🔒</div>
          <h3>Secure Booking</h3>
          <p>Your safety and security is our top priority</p>
        </div>
      </div>
    </div>
  );
};

export default Home;
EOF

# Upgrade Home CSS
cat <<EOF > src/components/home/home.css
.home-container {
  min-height: 100vh;
  background: linear-gradient(to bottom, #ffffff, #f8f9fa);
}

.hero-section {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 6rem 8rem;
  gap: 4rem;
}

.hero-content {
  flex: 1;
}

.hero-title {
  font-size: 4rem;
  font-weight: 700;
  color: #1a1a1a;
  margin-bottom: 1.5rem;
  line-height: 1.2;
}

.hero-subtitle {
  font-size: 1.5rem;
  color: #666;
  margin-bottom: 2.5rem;
}

.cta-button {
  padding: 1rem 2rem;
  font-size: 1.1rem;
  background: linear-gradient(45deg, #2563eb, #3b82f6);
  color: white;
  border: none;
  border-radius: 12px;
  cursor: pointer;
  transition: transform 0.3s ease;
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.cta-button:hover {
  transform: translateY(-2px);
}

.arrow {
  transition: transform 0.3s ease;
}

.cta-button:hover .arrow {
  transform: translateX(4px);
}

.hero-image {
  flex: 1;
  display: flex;
  justify-content: center;
}

.hero-image img {
  max-width: 100%;
  height: auto;
  border-radius: 24px;
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.1);
}

.features-section {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 2rem;
  padding: 4rem 8rem;
  background: white;
}

.feature-card {
  padding: 2rem;
  background: white;
  border-radius: 16px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.05);
  transition: transform 0.3s ease;
  text-align: center;
}

.feature-card:hover {
  transform: translateY(-5px);
}

.feature-icon {
  font-size: 2.5rem;
  margin-bottom: 1rem;
}

.feature-card h3 {
  font-size: 1.25rem;
  color: #1a1a1a;
  margin-bottom: 0.5rem;
}

.feature-card p {
  color: #666;
  line-height: 1.6;
}

@media (max-width: 1024px) {
  .hero-section {
    padding: 4rem 2rem;
    flex-direction: column;
    text-align: center;
  }

  .hero-title {
    font-size: 3rem;
  }

  .features-section {
    padding: 2rem;
  }
}

@media (max-width: 768px) {
  .hero-title {
    font-size: 2.5rem;
  }

  .hero-subtitle {
    font-size: 1.25rem;
  }
}
EOF

# Upgrade RoomsList component
cat <<EOF > src/components/roomslist/RoomsList.js
import { useState } from "react";
import "./roomslist.css";

function RoomsList() {
    const [rooms] = useState([
        {
            id: 1,
            name: "Detroit Condo Room",
            description: "This condo sits next to the Ford Field arena where the Detroit Lions play! The room is 15x16 sqft and comes furnished with a bed, nightstand, and television.",
            location: "Detroit",
            state: "MI",
            price: 75,
            imageUrl: "https://images.unsplash.com/photo-1522708323590-d24dbb6b0267?ixlib=rb-4.0.3&auto=format&fit=crop&w=800&q=80",
            amenities: ["WiFi", "TV", "Kitchen", "Parking"]
        },
        {
            id: 2,
            name: "Seattle Waterfront Studio",
            description: "Enjoy stunning views of Puget Sound from this modern studio apartment. Walking distance to Pike Place Market and the Space Needle.",
            location: "Seattle",
            state: "WA",
            price: 120,
            imageUrl: "https://images.unsplash.com/photo-1502672260266-1c1ef2d93688?ixlib=rb-4.0.3&auto=format&fit=crop&w=800&q=80",
            amenities: ["Ocean View", "WiFi", "Gym", "Pool"]
        },
        {
            id: 3,
            name: "Miami Beach Getaway",
            description: "Sun-drenched room with private bathroom in a luxury condo. Just steps from the beach and South Beach nightlife.",
            location: "Miami",
            state: "FL",
            price: 95,
            imageUrl: "https://images.unsplash.com/photo-1560448204-e02f11c3d0e2?ixlib=rb-4.0.3&auto=format&fit=crop&w=800&q=80",
            amenities: ["Beach Access", "WiFi", "Pool", "Spa"]
        }
    ]);

    return (
        <div className="rooms-page">
            <div className="rooms-header">
                <h1>Available Rooms</h1>
                <p>Find your perfect temporary home</p>
                <div className="search-container">
                    <input type="text" placeholder="Search by location..." className="search-input" />
                    <button className="search-button">Search</button>
                </div>
            </div>
            
            <div className="rooms-grid">
                {rooms.map((room) => (
                    <div className="room-card" key={room.id}>
                        <div 
                            className="room-image" 
                            style={{
                                backgroundImage: \`url("\${room.imageUrl}")\`,
                                backgroundSize: 'cover',
                                backgroundPosition: 'center'
                            }}
                        >
                            <div className="price-tag">\${room.price}/night</div>
                        </div>
                        <div className="room-details">
                            <h2>{room.name}</h2>
                            <p className="location">
                                <span className="location-icon">📍</span>
                                {room.location}, {room.state}
                            </p>
                            <p className="description">{room.description}</p>
                            <div className="amenities">
                                {room.amenities.map((amenity, index) => (
                                    <span key={index} className="amenity-tag">{amenity}</span>
                                ))}
                            </div>
                            <button className="book-button">Book Now</button>
                        </div>
                    </div>
                ))}
            </div>
        </div>
    );
}

export default RoomsList;
EOF

# Upgrade RoomsList CSS
cat <<EOF > src/components/roomslist/roomslist.css
.rooms-page {
    min-height: 100vh;
    padding: 2rem;
    background: linear-gradient(to bottom, #f8f9fa, #ffffff);
}

.rooms-header {
    text-align: center;
    margin-bottom: 3rem;
    padding: 2rem 0;
}

.rooms-header h1 {
    font-size: 2.5rem;
    color: #1a1a1a;
    margin-bottom: 1rem;
}

.rooms-header p {
    font-size: 1.2rem;
    color: #666;
    margin-bottom: 2rem;
}

.search-container {
    display: flex;
    justify-content: center;
    gap: 1rem;
    max-width: 600px;
    margin: 0 auto;
}

.search-input {
    padding: 1rem 1.5rem;
    border: 2px solid #e2e8f0;
    border-radius: 12px;
    font-size: 1rem;
    width: 100%;
    transition: border-color 0.3s ease;
}

.search-input:focus {
    outline: none;
    border-color: #3b82f6;
}

.search-button {
    padding: 1rem 2rem;
    background: linear-gradient(45deg, #2563eb, #3b82f6);
    color: white;
    border: none;
    border-radius: 12px;
    cursor: pointer;
    transition: transform 0.3s ease;
}

.search-button:hover {
    transform: translateY(-2px);
}

.rooms-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(350px, 1fr));
    gap: 2rem;
    max-width: 1400px;
    margin: 0 auto;
}

.room-card {
    background: white;
    border-radius: 20px;
    overflow: hidden;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.05);
    transition: transform 0.3s ease;
}

.room-card:hover {
    transform: translateY(-5px);
}

.room-image {
    height: 250px;
    width: 100%;
    position: relative;
}

.price-tag {
    position: absolute;
    right: 1rem;
    top: 1rem;
    background: rgba(0, 0, 0, 0.7);
    color: white;
    padding: 0.5rem 1rem;
    border-radius: 20px;
    font-weight: bold;
}

.room-details {
    padding: 1.5rem;
}

.room-details h2 {
    font-size: 1.5rem;
    color: #1a1a1a;
    margin-bottom: 0.5rem;
}

.location {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    color: #666;
    margin-bottom: 1rem;
}

.location-icon {
    font-size: 1.2rem;
}

.description {
    color: #4a5568;
    margin-bottom: 1rem;
    line-height: 1.6;
}

.amenities {
    display: flex;
    flex-wrap: wrap;
    gap: 0.5rem;
    margin-bottom: 1.5rem;
}

.amenity-tag {
    background: #f7fafc;
    color: #4a5568;
    padding: 0.3rem 0.8rem;
    border-radius: 20px;
    font-size: 0.9rem;
}

.book-button {
    width: 100%;
    padding: 1rem;
    background: linear-gradient(45deg, #2563eb, #3b82f6);
    color: white;
    border: none;
    border-radius: 12px;
    cursor: pointer;
    transition: transform 0.3s ease;
    font-weight: bold;
}

.book-button:hover {
    transform: translateY(-2px);
}

@media (max-width: 768px) {
    .rooms-header h1 {
        font-size: 2rem;
    }

    .rooms-grid {
        grid-template-columns: 1fr;
    }

    .search-container {
        flex-direction: column;
    }

    .search-button {
        width: 100%;
    }
}
EOF

# Add the files to git and commit
git add src/components/home/Home.js src/components/home/home.css src/components/roomslist/RoomsList.js src/components/roomslist/roomslist.css src/images
git commit -m "Add workshop completion banner and enhance UI"
git push origin main
```

### 2️⃣ Monitor Pipeline Execution

1. Navigate to the AWS CodePipeline console:

   ```
   https://us-east-1.console.aws.amazon.com/codesuite/codepipeline/pipelines
   ```

2. Watch as your pipeline progresses through each stage:
   - **Source**: Pulling your latest code with the enhanced UI
   - **Build**: Building the Docker image with Docker Build Cloud
   - **Security Scan**: Scanning the image with Docker Scout
   - **Deploy**: Deploying to Amazon ECS

![pipeline-execution](/images/pipeline-execution.png)

## 🔍 Examining Docker Scout Results

Once the pipeline reaches the Security Scan stage, examine the Docker Scout results:

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
# Get the load balancer URL
LB_URL=$(aws ecs describe-services --cluster rent-a-room-cluster --services rent-a-room-service \
  --query 'services[0].loadBalancers[0].targetGroupArn' --output text | \
  xargs -I {} aws elbv2 describe-target-groups --target-group-arns {} \
  --query 'TargetGroups[0].LoadBalancerArns[0]' --output text | \
  xargs -I {} aws elbv2 describe-load-balancers --load-balancer-arns {} \
  --query 'LoadBalancers[0].DNSName' --output text)

echo "✅ Application URL: http://$LB_URL"
```

Visit the application URL to see your deployed application with the enhanced UI and workshop completion banner!

## 🎮 Interactive Challenge: Customize Your App Further

Now that you understand how the CI/CD pipeline works, try one of these fun customizations:

1. **Change the Color Scheme**: Modify the CSS to use your favorite colors
2. **Add a New Room**: Add another room listing with a unique description
3. **Update the Logo**: Change the navbar brand from "Rent A Room" to something creative

Make your changes, commit them, and watch the pipeline automatically deploy your customized version!

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

## 🎉 Congratulations!

You've successfully built a complete end-to-end CI/CD pipeline that leverages the best of AWS and Docker technologies. This modern approach to application development and deployment will help you deliver secure, scalable applications faster than ever before.

Remember to clean up your resources when you're done with the workshop to avoid unnecessary charges.
