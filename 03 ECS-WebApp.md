To use AWS ECS (Elastic Container Service) for running a sample Docker image of a web application, you will need to create and push a Docker image, then use ECS to deploy and manage it. Here's a step-by-step guide to help you with the process.

1. Create a Sample Web Application

Let’s create a simple Node.js web application that will be Dockerized and deployed using ECS.

Create a Dockerfile for Node.js Web App

mkdir ecs-sample-app
cd ecs-sample-app

Create the app.js file:

const express = require('express');
const app = express();
const port = 3000;

app.get('/', (req, res) => {
  res.send('Hello from ECS!');
});

app.listen(port, () => {
  console.log(`App running at http://localhost:${port}`);
});

Create a package.json file:

{
  "name": "ecs-sample-app",
  "version": "1.0.0",
  "description": "Sample Node.js app for ECS",
  "main": "app.js",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "express": "^4.17.1"
  }
}

Create a Dockerfile:

# Use the official Node.js image from Docker Hub
FROM node:14

# Set the working directory inside the container
WORKDIR /usr/src/app

# Copy package.json and package-lock.json
COPY package*.json ./

# Install the app dependencies
RUN npm install

# Copy the rest of the application code to the working directory
COPY . .

# Expose the port the app runs on
EXPOSE 3000

# Command to run the application
CMD ["npm", "start"]

2. Build and Test the Docker Image Locally

1. Build the Docker image locally:

docker build -t ecs-sample-app .


2. Run the Docker image locally:

docker run -p 3000:3000 ecs-sample-app


3. Open your browser and navigate to http://localhost:3000 to see the "Hello from ECS!" message.



3. Push the Docker Image to Amazon ECR

To deploy the Docker image using ECS, you need to push it to Amazon ECR (Elastic Container Registry).

1. Create an ECR Repository:

Go to the ECR console and click Create repository.

Name the repository ecs-sample-app and click Create.



2. Authenticate Docker to Your ECR: Use the AWS CLI to authenticate Docker with your ECR. Replace <region> with your AWS region.

aws ecr get-login-password --region <region> | docker login --username AWS --password-stdin <aws-account-id>.dkr.ecr.<region>.amazonaws.com


3. Tag and Push the Image: Tag your Docker image so it points to the ECR repository you created.

docker tag ecs-sample-app:latest <aws-account-id>.dkr.ecr.<region>.amazonaws.com/ecs-sample-app:latest

Push the image to ECR:

docker push <aws-account-id>.dkr.ecr.<region>.amazonaws.com/ecs-sample-app:latest



4. Create an ECS Cluster

1. Create a Cluster:

Go to the ECS console and click Clusters > Create Cluster.

Choose EC2 Linux + Networking.

Name the cluster ecs-sample-cluster.

Configure the number of EC2 instances, instance type, and key pair.

Click Create to launch the ECS cluster.




5. Create a Task Definition

1. Create a Task Definition:

Go to the ECS console, click Task Definitions, and then click Create new Task Definition.

Choose EC2 for the launch type.

Name the task definition ecs-sample-task.

Under Container Definitions, click Add container and configure the container:

Container name: ecs-sample-container.

Image: Use the image URI from your ECR repository (e.g., <aws-account-id>.dkr.ecr.<region>.amazonaws.com/ecs-sample-app:latest).

Memory Limits: Set to 512 (soft) and 1024 (hard).

Port Mappings: Set the Host port and Container port to 3000.


Click Create.




6. Run the Task on ECS

1. Create a Service:

Go to the ECS console, click Clusters, and select your cluster.

Click Create and choose EC2 for the launch type.

Under Task Definition, choose the task you just created (ecs-sample-task).

Specify the number of tasks (e.g., 1).

Under Load balancing, choose None.

Click Next and then Create Service.



2. Verify the Service:

Go to the ECS console and navigate to the Tasks tab in your cluster to see your running task.

Get the Public IP of the EC2 instance from the EC2 console.




7. Access the Application

Once the task is running, access the application by going to the public IP of the EC2 instance on port 3000. For example:

http://<ec2-public-ip>:3000

Summary

You have successfully:

1. Created a Docker image of a simple Node.js web application.


2. Pushed the Docker image to Amazon ECR.


3. Deployed the image to ECS using an EC2-backed ECS cluster.


4. Accessed the web application through the public IP of your EC2 instance.



Let me know if you'd like to expand this example to include an Application Load Balancer (ALB) or an auto-scaling setup!

