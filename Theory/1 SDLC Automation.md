CICD tools:

### **CodeCommit**:
- **Overview**: A fully managed source control service that supports Git. It allows teams to collaborate on code in a secure and scalable way.
- **Options**: You can manage multiple repositories and branches. It supports Git functionalities like cloning, committing, branching, and merging.
- **Securing Repositories and Branches**: Use AWS IAM to control access, enforce branch protection rules, and enable encryption with AWS KMS.
- **Triggers and Notifications**: Configure triggers for actions such as push events or pull requests. Notifications can be sent via SNS or Lambda.

### **CodeBuild**:
- **Overview**: A fully managed build service that compiles your source code, runs tests, and produces deployable artifacts.
- **buildspec.yaml**: This file defines build commands, environment variables, phases (install, pre-build, build, post-build), and artifacts.
- **Docker & ECR**: You can build and push Docker images to Amazon ECR using `buildspec.yaml`.
- **Environment Variables & Parameter Store**: Pass environment variables or secrets from the AWS Parameter Store into your build process.
- **Artifacts & S3**: Store build artifacts in S3 buckets.
- **Events & Logging**: Integrate with CloudWatch for detailed logging of build events and phases.

### **CodeDeploy**:
- **Overview**: Automates application deployments to EC2 instances, Lambda, or on-premise servers.
- **Application Deployment Groups**: Define which instances or environments will receive the deployment.
- **Deployment Configurations**: Choose strategies like canary or linear deployments, especially for Lambda functions.
- **Hooks & Environment Variables**: Use lifecycle hooks to run scripts before, during, or after deployments, and pass environment variables.
- **Rollbacks**: Set up automatic rollbacks if the deployment fails or alarms are triggered.
- **Deploy to AWS Lambda**: Allows you to manage Lambda deployments with support for advanced strategies like canary releases.

### **CodePipeline**:
- **Overview**: Automates the end-to-end release process by orchestrating CodeCommit, CodeBuild, CodeDeploy, and other tools.
- **Integrating CodeCommit, CodeBuild, & CodeDeploy**: CodePipeline can automatically pull changes from CodeCommit, build using CodeBuild, and deploy with CodeDeploy.
- **Manual Approval Steps**: You can insert manual approval steps between stages for greater control.
- **Stage Actions**: Define actions like source, build, test, deploy, and approval in each stage.
- **All Integrations**: Supports a variety of integrations, including third-party tools like Jenkins, GitHub, and Bitbucket.
