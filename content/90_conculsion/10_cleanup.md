+++
title = "Cleanup"
chapter = false
weight = 101
+++

# 🧹 Comprehensive Cleanup

To prevent unexpected charges to your AWS account, it's essential to clean up all resources created during this workshop. We'll create and run a cleanup script that automatically identifies and removes all workshop resources.

## 🚀 Creating and Running the Cleanup Script

Copy and paste the following commands to create, make executable, and run the cleanup script:

```bash
# Create the cleanup script
cat << 'EOF' > cleanup-workshop.sh
#!/bin/bash
echo "🧹 Starting comprehensive workshop cleanup..."

# Find and delete all CloudFormation stacks related to the workshop
echo "🔍 Finding CloudFormation stacks..."
PIPELINE_STACK=$(aws cloudformation list-stacks --stack-status-filter CREATE_COMPLETE UPDATE_COMPLETE --query "StackSummaries[?contains(StackName, 'ECSPipeline') || contains(StackName, 'Pipeline') || contains(StackName, 'mod-')].StackName" --output text)

if [ -n "$PIPELINE_STACK" ]; then
  echo "🗑️ Deleting CloudFormation stack(s): $PIPELINE_STACK"
  for stack in $PIPELINE_STACK; do
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
    
    # Delete the cluster
    echo "🗑️ Deleting ECS cluster $CLUSTER_NAME..."
    aws ecs delete-cluster --cluster $CLUSTER_NAME
  done
else
  echo "ℹ️ No matching ECS clusters found"
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
S3_BUCKETS=$(aws s3api list-buckets --query "Buckets[?contains(Name, 'codepipeline') || contains(Name, 'artifact')].Name" --output text)

if [ -n "$S3_BUCKETS" ]; then
  echo "🗑️ Deleting S3 buckets..."
  for bucket in $S3_BUCKETS; do
    echo "🗑️ Emptying and deleting S3 bucket $bucket..."
    aws s3 rm s3://$bucket --recursive
    aws s3api delete-bucket --bucket $bucket
  done
else
  echo "ℹ️ No matching S3 buckets found"
fi

# Find and delete CodeStar connections
echo "🔍 Finding CodeStar connections..."
CONNECTIONS=$(aws codestar-connections list-connections --query "Connections[?ConnectionName!=null].ConnectionArn" --output text)

if [ -n "$CONNECTIONS" ]; then
  echo "🗑️ Deleting CodeStar connections..."
  for connection in $CONNECTIONS; do
    echo "🗑️ Deleting CodeStar connection $connection..."
    aws codestar-connections delete-connection --connection-arn $connection
  done
else
  echo "ℹ️ No CodeStar connections found"
fi

# Find and delete Secrets Manager secrets
echo "🔍 Finding Secrets Manager secrets..."
SECRETS=$(aws secretsmanager list-secrets --query "SecretList[?contains(Name, 'docker') || contains(Name, 'github')].Name" --output text)

if [ -n "$SECRETS" ]; then
  echo "🗑️ Deleting Secrets Manager secrets..."
  for secret in $SECRETS; do
    echo "🗑️ Deleting secret $secret..."
    aws secretsmanager delete-secret --secret-id $secret --force-delete-without-recovery
  done
else
  echo "ℹ️ No matching Secrets Manager secrets found"
fi

# Find and delete IAM roles
echo "🔍 Finding IAM roles related to the workshop..."
IAM_ROLES=$(aws iam list-roles --query "Roles[?contains(RoleName, 'CodeBuild') || contains(RoleName, 'CodePipeline') || contains(RoleName, 'ECS')].RoleName" --output text)

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
  echo "ℹ️ No matching IAM roles found"
fi

# Clean up local repository
echo "🧹 Cleaning up local Git repository..."
if [ -d "Rent-A-Room" ]; then
  cd Rent-A-Room
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

## 📋 What the Cleanup Script Does

The script automatically:

1. **Finds and deletes CloudFormation stacks** related to the workshop
2. **Cleans up ECS resources** including clusters and services
3. **Removes CodeBuild projects** created during the workshop
4. **Deletes CodePipeline pipelines** used for CI/CD
5. **Empties and removes S3 buckets** used for artifacts
6. **Deletes CodeStar connections** to GitHub
7. **Removes Secrets Manager secrets** for Docker Hub and GitHub
8. **Cleans up IAM roles** created for the workshop services
9. **Provides guidance** on cleaning up your GitHub fork

## ⚠️ Manual Verification

After running the script, it's always a good practice to verify in the AWS Console that all resources have been properly deleted:

1. Check the CloudFormation console for any remaining stacks
2. Verify in the ECS console that clusters and services are removed
3. Check CodePipeline and CodeBuild for any remaining resources
4. Look for any S3 buckets that might have been created during the workshop

## 🎉 Workshop Completion

Congratulations on completing the AWS and Docker: Better Together workshop! You've learned how to:

- Build a complete CI/CD pipeline using AWS services and Docker technologies
- Implement multi-architecture container builds with Docker Build Cloud
- Integrate security scanning with Docker Scout
- Deploy containerized applications to Amazon ECS
- Define infrastructure as code with AWS CloudFormation

We hope you found this workshop valuable and that you'll apply these techniques in your own projects!
