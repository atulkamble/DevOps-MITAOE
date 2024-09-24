You're diving into some powerful AWS features! Here’s a brief overview of the topics you're exploring:

### Advanced CloudFormation:
- **User Data & cfn-init**: Use EC2 instance metadata (User Data) to run scripts at instance launch. `cfn-init` helps with configuring software and services on instances.
- **cfn-signal & Wait Conditions**: `cfn-signal` is used to signal CloudFormation that a resource setup (like an EC2 instance) is complete. It’s crucial for coordinating resource creation and avoiding rollback when something isn’t ready.
- **Rollbacks**: Automatically reverts to a previous state if the stack fails during an update or creation.
- **Nested Tasks**: Break complex stacks into smaller, reusable nested stacks.
- **Change Sets**: Preview changes to your stack before applying them.
- **Deletion Policy**: Preserve or delete resources (like RDS snapshots) when a stack is deleted.
- **Lambda Functions**: Use CloudFormation to automate deployment of Lambda functions.
- **Drift Detection**: Detect changes made to your stack resources outside of CloudFormation.

### Advanced Elastic Beanstalk:
- **Saved Configurations**: Save and reuse custom configurations for environments.
- **.ebextensions**: Customize instances using configuration files to install software or configure resources like databases.
- **Rolling Update Strategies**: Ensure zero downtime updates by applying rolling updates across instances.
- **Swap URL**: Seamlessly switch URLs between environments for production deployment.
- **Worker Environments**: Background job processing via worker environments.

### Advanced Lambda:
- **Security**: Implement IAM roles, resource policies, and VPC for security.
- **Environment Variables**: Store sensitive data like API keys, and manage them securely using KMS and SSM.
- **Versions & Aliases**: Manage Lambda function versions, and use aliases to route traffic to specific versions, including canary releases for gradual rollouts.
- **SAM Framework**: Simplify the development and deployment of serverless applications with AWS SAM.

Let me know if you'd like to dive deeper into any of these areas!