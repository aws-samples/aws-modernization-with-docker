---
title: "Deploy to Amazon ECS"
chapter: true
weight: 61
---

# 🚀 Deploying to Amazon ECS

## **📚 Understanding ECS Concepts**

### What is ECS?

Amazon Elastic Container Service (ECS) is AWS's container orchestration service. Think of it as a system that manages where and how your Docker containers run in the cloud.

## **🚀 ECS Launch Types**

| Launch Type    | You Manage                  | Best For                 | Common Use Cases                                      |
|----------------|-----------------------------|--------------------------|-------------------------------------------------------|
| **AWS Fargate**| Nothing - fully managed     | Teams focused on apps    | • Microservices • Web applications • API backends     |
| **EC2**        | EC2 instances & capacity     | Infrastructure control   | • Legacy apps • GPU workloads • Cost optimization     |

**AWS Fargate (Serverless)**
- No servers to manage or maintain
- Pay only for resources your containers use
- Automatic scaling and high availability
- Perfect for teams that want to focus on application development

**Amazon EC2**
- Full control over infrastructure
- Custom instance types and configurations
- Access to instance operating system
- Ideal when you need specific hardware or compliance requirements

### Key ECS Components

#### 1. ECS Cluster

- A logical grouping of resources
- Like an apartment building that houses your containers
- Can contain multiple services and tasks

#### 2. Task Definition

Think of this as the blueprint for your application. It includes:

- Which container image to use
- How much CPU and memory to allocate
- Port mappings
- Environment variables
- Storage configurations

Example Task Definition components:
```json
{
    "family": "web-app",
    "containerDefinitions": [{
        "name": "web",          // Container name
        "image": "nginx:latest", // Docker image
        "cpu": 256,             // CPU units
        "memory": 512,          // Memory in MB
        "portMappings": [{      // Port configurations
            "containerPort": 80
        }]
    }]
}
```

#### 3. ECS Service

- Ensures your desired number of tasks are always running
- Handles load balancing and scaling
- Restarts failed containers automatically

Think of it as a restaurant manager who:

- Makes sure there are always enough servers (tasks)
- Distributes customers (traffic) evenly
- Replaces sick staff (failed containers)

### 🔄 How It All Works Together

```mermaid
graph TD
    A[ECS Cluster] --> B[ECS Service]
    B --> C[Task Definition]
    C --> D[Container 1]
    C --> E[Container 2]
    C --> F[Container N]
```

1. **Cluster** provides the infrastructure
2. **Service** maintains desired task count
3. **Task Definition** specifies how containers run
4. **Containers** run your application

## **📌 What We'll Do**

In this section, we will:

- ✅ Create an ECS Cluster
- ✅ Configure a Task Definition
- ✅ Deploy an ECS Service
- ✅ Access our application

## **1️⃣ Create an ECS Cluster**

### Using AWS Console:

1. Open the [Amazon ECS console](https://console.aws.amazon.com/ecs/)
2. Click **Create Cluster**
3. Select **AWS Fargate** (Serverless)
4. Configure basic settings:
   ```
   Cluster Name: rent-a-room-cluster
   VPC: Default VPC
   ```
5. Click **Create**

### Using AWS CLI:

```bash
aws ecs create-cluster --cluster-name rent-a-room-cluster
```

## **2️⃣ Create Task Definition**

### Using AWS Console:

1. In ECS console, go to **Task Definitions**
2. Click **Create new Task Definition**
3. Configure settings:
   ```
   Family: rent-a-room-task
   Launch type: FARGATE
   Task Role: ecsTaskExecutionRole
   Task memory: 0.5GB
   Task CPU: 0.25 vCPU
   ```
4. Add container:
   ```
   Container name: rent-a-room
   Image: YOUR_DOCKERHUB_USERNAME/rent-a-room:latest
   Port mappings: 80
   ```
5. Click **Create**

### Using AWS CLI:

Save this as `task-definition.json`:
```json
{
    "family": "rent-a-room-task",
    "networkMode": "awsvpc",
    "requiresCompatibilities": ["FARGATE"],
    "cpu": "256",
    "memory": "512",
    "containerDefinitions": [{
        "name": "rent-a-room",
        "image": "YOUR_DOCKERHUB_USERNAME/rent-a-room:latest",
        "portMappings": [{
            "containerPort": 80,
            "protocol": "tcp"
        }]
    }]
}
```

Then run:
```bash
aws ecs register-task-definition --cli-input-json file://task-definition.json
```

## **3️⃣ Create ECS Service**

### Using AWS Console:

1. In your cluster, click **Create Service**
2. Configure service:
   ```
   Launch type: FARGATE
   Service name: rent-a-room-service
   Task Definition: rent-a-room-task
   Number of tasks: 1
   ```
3. Configure networking:
   ```
   VPC: Default VPC
   Subnets: Select all available
   Security group: Create new (Allow inbound port 80)
   ```
4. Click **Create Service**

### Using AWS CLI:

```bash
# Get default VPC ID
VPC_ID=$(aws ec2 describe-vpcs \
    --filters Name=isDefault,Values=true \
    --query 'Vpcs[0].VpcId' \
    --output text)

# Get first subnet ID from default VPC
SUBNET_ID=$(aws ec2 describe-subnets \
    --filters Name=vpc-id,Values=$VPC_ID \
    --query 'Subnets[0].SubnetId' \
    --output text)

# Create security group
SECURITY_GROUP_ID=$(aws ec2 create-security-group \
    --group-name ecs-rent-a-room-sg \
    --description "Security group for Rent-A-Room ECS service" \
    --vpc-id $VPC_ID \
    --query 'GroupId' \
    --output text)

# Add inbound rule for port 80
aws ec2 authorize-security-group-ingress \
    --group-id $SECURITY_GROUP_ID \
    --protocol tcp \
    --port 80 \
    --cidr 0.0.0.0/0

# Create the ECS service with dynamic values
aws ecs create-service \
    --cluster rent-a-room-cluster \
    --service-name rent-a-room-service \
    --task-definition rent-a-room-task \
    --desired-count 1 \
    --launch-type FARGATE \
    --network-configuration "awsvpcConfiguration={subnets=[$SUBNET_ID],securityGroups=[$SECURITY_GROUP_ID],assignPublicIp=ENABLED}"
```

## **4️⃣ Access Your Application**

### Using AWS Console:
1. In the [Amazon ECS console](https://console.aws.amazon.com/ecs/), navigate to your service
2. Click on the running task
3. Find the **Public IP**
4. Access your application: `http://[PUBLIC_IP]`

### Using AWS CLI:

```bash
# Get the task ARN
TASK_ARN=$(aws ecs list-tasks \
    --cluster rent-a-room-cluster \
    --service-name rent-a-room-service \
    --query 'taskArns[0]' \
    --output text)

# Get the ENI ID attached to the task
ENI_ID=$(aws ecs describe-tasks \
    --cluster rent-a-room-cluster \
    --tasks $TASK_ARN \
    --query 'tasks[0].attachments[0].details[?name==`networkInterfaceId`].value' \
    --output text)

# Get the public IP
PUBLIC_IP=$(aws ec2 describe-network-interfaces \
    --network-interface-ids $ENI_ID \
    --query 'NetworkInterfaces[0].Association.PublicIp' \
    --output text)

echo "Your application is available at: http://$PUBLIC_IP"
```

## **🔍 Monitoring**

Monitor your application using:
- ECS console dashboard
- CloudWatch metrics
- Container insights (if enabled)

## **💡 Pro Tips**

- Start with Fargate for simplicity
- Use Task Definitions for repeatable deployments
- Implement service auto-scaling for production
- Consider using Application Load Balancer for web applications
- Enable Container Insights for better monitoring

## **🎯 Key Takeaways**

| Concept | Understanding |
|---------|---------------|
| **ECS Cluster** | The foundation that hosts your containers - like a virtual data center |
| **Launch Types** | Fargate (serverless) vs EC2 (server-based) - choose based on your needs |
| **Task Definition** | The blueprint that defines how your container should run |
| **ECS Service** | The manager that maintains your desired container state |
| **Architecture** | Understanding how all components work together for container orchestration |

## **🚀 Next Steps**

Now that you have:
- ✅ Created an ECS cluster
- ✅ Deployed a containerized application
- ✅ Learned about ECS components
- ✅ Accessed your running application

✨ Continue to CI/CD Pipeline Setup ▶️
