+++
title = "Cleanup"
chapter = false
weight = 101
+++

# 🧹 Comprehensive Cleanup

To prevent unexpected charges to your AWS account, it's essential to clean up all resources created during this workshop. We'll create and run a cleanup script that automatically identifies and removes all workshop resources.

## 🚀 Creating and Running the Cleanup Script

Navigate to the AWS Management Console and ensure you're in the correct region where your CloudFormation stack was previously deployed. Then, open CloudShell by clicking the icon located in the top navigation bar:
![CloudShell](/images/CloudShellTopBar.png)

Copy and paste the following commands to create, make executable, and run the cleanup script:

```bash
cat << 'EOF' > cleanup-workshop.sh
#!/bin/bash
echo "🧹 Starting comprehensive workshop cleanup..."

# Find and delete all CloudFormation stacks related to the workshop
echo "🔍 Finding CloudFormation stacks..."
STACKS_TO_DELETE=$(aws cloudformation list-stacks --stack-status-filter CREATE_COMPLETE UPDATE_COMPLETE --query "StackSummaries[?contains(StackName, 'docker-workshop') || contains(StackName, 'ECSPipeline') || contains(StackName, 'Pipeline') || contains(StackName, 'mod-') || contains(StackName, 'vscode-server')].StackName" --output text)

if [ -n "$STACKS_TO_DELETE" ]; then
  echo "🗑️ Deleting CloudFormation stack(s): $STACKS_TO_DELETE"
  for stack in $STACKS_TO_DELETE; do
    aws cloudformation delete-stack --stack-name $stack
    echo "⏳ Waiting for stack $stack to be deleted..."
    aws cloudformation wait stack-delete-complete --stack-name $stack
    echo "✅ Stack $stack deleted successfully"
  done
else
  echo "ℹ️ No matching CloudFormation stacks found"
fi

# Find and delete ECS resources
echo "🔍 Finding ECS clusters..."
ECS_CLUSTERS=$(aws ecs list-clusters --query "clusterArns[?contains(@, 'rent-a-room') || contains(@, 'workshop')]" --output text)

if [ -n "$ECS_CLUSTERS" ]; then
  echo "🗑️ Deleting ECS clusters and services..."
  for cluster in $ECS_CLUSTERS; do
    # Get cluster name from ARN
    CLUSTER_NAME=$(echo $cluster | awk -F/ '{print $2}')

    # List services in the cluster
    SERVICES=$(aws ecs list-services --cluster $CLUSTER_NAME --query "serviceArns[]" --output text)

    # Delete services first
    if [ -n "$SERVICES" ]; then
      for service in $SERVICES; do
        SERVICE_NAME=$(echo $service | awk -F/ '{print $3}')
        echo "🗑️ Scaling down service $SERVICE_NAME in cluster $CLUSTER_NAME..."
        aws ecs update-service --cluster $CLUSTER_NAME --service $SERVICE_NAME --desired-count 0
        echo "🗑️ Deleting service $SERVICE_NAME from cluster $CLUSTER_NAME..."
        aws ecs delete-service --cluster $CLUSTER_NAME --service $SERVICE_NAME --force
      done
    fi

    # Wait for services to be deleted
    echo "⏳ Waiting for services to be deleted..."
    sleep 30

    # Delete the cluster
    echo "🗑️ Deleting ECS cluster $CLUSTER_NAME..."
    aws ecs delete-cluster --cluster $CLUSTER_NAME
  done
else
  echo "ℹ️ No matching ECS clusters found"
fi

# Find and delete Load Balancers
echo "🔍 Finding Load Balancers..."
LOAD_BALANCERS=$(aws elbv2 describe-load-balancers --query "LoadBalancers[?contains(LoadBalancerName, 'rent-a-room') || contains(LoadBalancerName, 'workshop')].LoadBalancerArn" --output text)

if [ -n "$LOAD_BALANCERS" ]; then
  echo "🗑️ Deleting Load Balancers..."
  for lb in $LOAD_BALANCERS; do
    echo "🗑️ Deleting Load Balancer $lb..."
    aws elbv2 delete-load-balancer --load-balancer-arn $lb
  done

  # Wait a bit for load balancers to be deleted
  echo "⏳ Waiting for load balancers to be deleted..."
  sleep 30
else
  echo "ℹ️ No matching Load Balancers found"
fi

# Find and delete Target Groups
echo "🔍 Finding Target Groups..."
TARGET_GROUPS=$(aws elbv2 describe-target-groups --query "TargetGroups[?contains(TargetGroupName, 'rent-a-room') || contains(TargetGroupName, 'workshop')].TargetGroupArn" --output text)

if [ -n "$TARGET_GROUPS" ]; then
  echo "🗑️ Deleting Target Groups..."
  for tg in $TARGET_GROUPS; do
    echo "🗑️ Deleting Target Group $tg..."
    aws elbv2 delete-target-group --target-group-arn $tg
  done
else
  echo "ℹ️ No matching Target Groups found"
fi

# Find and delete CodeBuild projects
echo "🔍 Finding CodeBuild projects..."
CODEBUILD_PROJECTS=$(aws codebuild list-projects --query "projects[?contains(@, 'docker') || contains(@, 'scout') || contains(@, 'build')]" --output text)

if [ -n "$CODEBUILD_PROJECTS" ]; then
  echo "🗑️ Deleting CodeBuild projects..."
  for project in $CODEBUILD_PROJECTS; do
    echo "🗑️ Deleting CodeBuild project $project..."
    aws codebuild delete-project --name $project
  done
else
  echo "ℹ️ No matching CodeBuild projects found"
fi

# Find and delete CodePipeline pipelines
echo "🔍 Finding CodePipeline pipelines..."
PIPELINES=$(aws codepipeline list-pipelines --query "pipelines[?contains(name, 'docker') || contains(name, 'Pipeline') || contains(name, 'ECS')].name" --output text)

if [ -n "$PIPELINES" ]; then
  echo "🗑️ Deleting CodePipeline pipelines..."
  for pipeline in $PIPELINES; do
    echo "🗑️ Deleting pipeline $pipeline..."
    aws codepipeline delete-pipeline --name $pipeline
  done
else
  echo "ℹ️ No matching CodePipeline pipelines found"
fi

# Find and delete S3 buckets
echo "🔍 Finding S3 buckets related to the workshop..."
S3_BUCKETS=$(aws s3api list-buckets --query "Buckets[?contains(Name, 'codepipeline') || contains(Name, 'artifact') || contains(Name, 'docker-workshop')].Name" --output text)

if [ -n "$S3_BUCKETS" ]; then
  echo "🗑️ Deleting S3 buckets..."
  for bucket in $S3_BUCKETS; do
    echo "🗑️ Emptying and deleting S3 bucket $bucket..."
    # Delete all versions of all objects
    echo "🗑️ Removing all object versions from bucket..."
    aws s3api list-object-versions --bucket $bucket --output json --query '{Objects: Versions[].{Key:Key,VersionId:VersionId}}' | grep -v "null" > /tmp/versions-$bucket.json
    if [ -s /tmp/versions-$bucket.json ]; then
      aws s3api delete-objects --bucket $bucket --delete file:///tmp/versions-$bucket.json
    fi

    # Delete all delete markers
    echo "🗑️ Removing all delete markers from bucket..."
    aws s3api list-object-versions --bucket $bucket --output json --query '{Objects: DeleteMarkers[].{Key:Key,VersionId:VersionId}}' | grep -v "null" > /tmp/markers-$bucket.json
    if [ -s /tmp/markers-$bucket.json ]; then
      aws s3api delete-objects --bucket $bucket --delete file:///tmp/markers-$bucket.json
    fi

    # Now delete the bucket itself
    echo "🗑️ Deleting bucket $bucket..."
    aws s3api delete-bucket --bucket $bucket

    # Clean up temporary files
    rm -f /tmp/versions-$bucket.json /tmp/markers-$bucket.json
  done
else
  echo "ℹ️ No matching S3 buckets found"
fi

# Find and delete Secrets Manager secrets
echo "🔍 Finding Secrets Manager secrets..."
SECRETS=$(aws secretsmanager list-secrets --query "SecretList[?contains(Name, 'docker') || contains(Name, 'github') || contains(Name, 'dockerhub')].Name" --output text)

if [ -n "$SECRETS" ]; then
  echo "🗑️ Deleting Secrets Manager secrets..."
  for secret in $SECRETS; do
    echo "🗑️ Deleting secret $secret..."
    aws secretsmanager delete-secret --secret-id $secret --force-delete-without-recovery
  done
else
  echo "ℹ️ No matching Secrets Manager secrets found"
fi

# Find and delete Auto Scaling policies and targets
echo "🔍 Finding Auto Scaling policies..."
SCALING_POLICIES=$(aws application-autoscaling describe-scaling-policies --service-namespace ecs --query "ScalingPolicies[?contains(ResourceId, 'rent-a-room')].PolicyARN" --output text 2>/dev/null || echo "")

if [ -n "$SCALING_POLICIES" ]; then
  echo "🗑️ Deleting Auto Scaling policies..."
  for policy in $SCALING_POLICIES; do
    echo "🗑️ Deleting Auto Scaling policy $policy..."
    aws application-autoscaling delete-scaling-policy --service-namespace ecs --policy-name $(echo $policy | awk -F/ '{print $NF}') --resource-id $(echo $policy | awk -F: '{print $6}' | awk -F/ '{print $2"/"$3}') --scalable-dimension ecs:service:DesiredCount
  done
else
  echo "ℹ️ No matching Auto Scaling policies found"
fi

echo "🔍 Finding Auto Scaling targets..."
SCALING_TARGETS=$(aws application-autoscaling describe-scalable-targets --service-namespace ecs --query "ScalableTargets[?contains(ResourceId, 'rent-a-room')].ResourceId" --output text 2>/dev/null || echo "")

if [ -n "$SCALING_TARGETS" ]; then
  echo "🗑️ Deregistering Auto Scaling targets..."
  for target in $SCALING_TARGETS; do
    echo "🗑️ Deregistering Auto Scaling target $target..."
    aws application-autoscaling deregister-scalable-target --service-namespace ecs --resource-id $target --scalable-dimension ecs:service:DesiredCount
  done
else
  echo "ℹ️ No matching Auto Scaling targets found"
fi

# Find and delete IAM roles (excluding AWS service-linked roles)
echo "🔍 Finding IAM roles related to the workshop..."
IAM_ROLES=$(aws iam list-roles --query "Roles[?(!contains(RoleName, 'AWSServiceRole')) && (contains(RoleName, 'CodeBuild') || contains(RoleName, 'CodePipeline') || contains(RoleName, 'ECS') || contains(RoleName, 'docker-workshop'))].RoleName" --output text)

if [ -n "$IAM_ROLES" ]; then
  echo "🗑️ Deleting IAM roles..."
  for role in $IAM_ROLES; do
    # Detach policies first
    POLICIES=$(aws iam list-attached-role-policies --role-name $role --query "AttachedPolicies[].PolicyArn" --output text)
    for policy in $POLICIES; do
      echo "🗑️ Detaching policy $policy from role $role..."
      aws iam detach-role-policy --role-name $role --policy-arn $policy
    done

    # Delete role
    echo "🗑️ Deleting IAM role $role..."
    aws iam delete-role --role-name $role
  done
else
  echo "ℹ️ No matching deletable IAM roles found"
fi

# Find and delete EC2 security groups
echo "🔍 Finding EC2 security groups related to the workshop..."
SECURITY_GROUPS=$(aws ec2 describe-security-groups --query "SecurityGroups[?contains(GroupName, 'rent-a-room') || contains(GroupName, 'workshop')].GroupId" --output text)

if [ -n "$SECURITY_GROUPS" ]; then
  echo "🗑️ Deleting EC2 security groups..."
  for sg in $SECURITY_GROUPS; do
    # Check for dependencies
    DEPENDENCIES=$(aws ec2 describe-network-interfaces --filters "Name=group-id,Values=$sg" --query 'NetworkInterfaces[].NetworkInterfaceId' --output text)

    if [ -n "$DEPENDENCIES" ]; then
      echo "⚠️ Security group $sg has dependencies: $DEPENDENCIES"
      echo "ℹ️ Please manually check and remove dependencies before deleting this security group"
    else
      echo "🗑️ Deleting security group $sg..."
      aws ec2 delete-security-group --group-id $sg
    fi
  done
else
  echo "ℹ️ No matching EC2 security groups found"
fi

# Clean up local repository
echo "🧹 Cleaning up local Git repository..."
if [ -d "/workshop/Rent-A-Room" ]; then
  cd /workshop/Rent-A-Room
  git remote -v | grep origin | grep -q github.com && echo "ℹ️ Consider deleting your GitHub fork of Rent-A-Room if no longer needed"
  cd ..
fi

echo "✅ Cleanup completed successfully!"
echo "🔍 Note: Please verify in the AWS Console that all resources have been properly deleted."
echo "💡 Tip: You may also want to delete your GitHub fork of the Rent-A-Room repository if you no longer need it."
EOF

# Make the script executable
chmod +x cleanup-workshop.sh

# Run the cleanup script
./cleanup-workshop.sh
```

Please note that in the above command you will need to use `q` on the keyboard to quite from the output of command output to go on to the next delete.

## 📋 What the Cleanup Script Does

The script automatically:

1. **Finds and deletes CloudFormation stacks** related to the workshop, including the VS Code server stack
2. **Cleans up ECS resources** including clusters and services
3. **Removes Load Balancers and Target Groups** created for the application
4. **Deletes CodeBuild projects** created during the workshop
5. **Removes CodePipeline pipelines** used for CI/CD
6. **Empties and removes S3 buckets** used for artifacts
7. **Deletes CodeStar connections** to GitHub
8. **Removes Secrets Manager secrets** for Docker Hub and GitHub
9. **Cleans up Auto Scaling policies and targets** configured for the ECS service
10. **Removes IAM roles** created for the workshop services
11. **Deletes EC2 security groups** created for the application
12. **Provides guidance** on cleaning up your GitHub fork

## ⚠️ Manual Verification

After running the script, it's always a good practice to verify in the AWS Console that all resources have been properly deleted:

1. Check the CloudFormation console for any remaining stacks
2. Verify in the ECS console that clusters and services are removed
3. Check the EC2 console for any remaining load balancers, target groups, or security groups
4. Look for any remaining CodePipeline, CodeBuild, or CodeStar resources
5. Verify that all S3 buckets related to the workshop have been deleted
6. Check Secrets Manager for any remaining secrets
7. Look for any remaining IAM roles or policies

## 🎉 Workshop Completion

Congratulations on completing the AWS and Docker: Better Together workshop! You've learned how to:

- Build a complete CI/CD pipeline using AWS services and Docker technologies
- Implement multi-architecture container builds with Docker Build Cloud
- Integrate security scanning with Docker Scout
- Deploy containerized applications to Amazon ECS
- Define infrastructure as code with AWS CloudFormation
- Implement advanced deployment strategies like blue/green and canary deployments

We hope you found this workshop valuable and that you'll apply these techniques in your own projects!
