Here's a simple sample application using AWS CodeCommit, CodeBuild, and CodeDeploy to automate the deployment of a web application on EC2. I'll provide a basic Node.js app for the demonstration.

1. Set Up CodeCommit

Create a repository in CodeCommit to store your application code.

1. Go to the CodeCommit console.


2. Click Create repository, name it node-app-repo, and set it up.


3. Clone the repository locally:

git clone https://git-codecommit.<region>.amazonaws.com/v1/repos/node-app-repo
cd node-app-repo



2. Sample Node.js Application

Create the following basic app structure:

app.js:

const http = require('http');

const server = http.createServer((req, res) => {
    res.statusCode = 200;
    res.setHeader('Content-Type', 'text/plain');
    res.end('Hello World\n');
});

server.listen(3000, '0.0.0.0', () => {
    console.log('Server running at http://0.0.0.0:3000/');
});

package.json:

{
  "name": "node-app",
  "version": "1.0.0",
  "description": "Sample Node.js app",
  "main": "app.js",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "express": "^4.17.1"
  }
}


3. Create BuildSpec for CodeBuild

Create a buildspec.yml file in the root of your repository to define the build steps.

buildspec.yml:

version: 0.2

phases:
  install:
    commands:
      - echo Installing Node.js
      - curl -sL https://rpm.nodesource.com/setup_14.x | sudo bash -
      - sudo yum install -y nodejs
  pre_build:
    commands:
      - echo Installing dependencies
      - npm install
  build:
    commands:
      - echo Build started on `date`
  post_build:
    commands:
      - echo Build completed on `date`
artifacts:
  files:
    - '**/*'


4. CodeDeploy AppSpec File

Create an appspec.yml file for CodeDeploy to define the deployment instructions.

appspec.yml:

version: 0.0
os: linux
files:
  - source: /
    destination: /home/ec2-user/node-app
hooks:
  AfterInstall:
    - location: scripts/start_server.sh
      timeout: 300
      runas: ec2-user


Create the scripts/start_server.sh file:

scripts/start_server.sh:

#!/bin/bash
cd /home/ec2-user/node-app
npm install
pm2 start app.js


Make sure to push your code to CodeCommit:

git add .
git commit -m "Initial commit"
git push origin main

5. Set Up CodeBuild

1. Go to the CodeBuild console and create a new project.


2. Under Source, select CodeCommit and choose your repository node-app-repo.


3. Under Buildspec, choose "Use a buildspec file."


4. Configure the environment with Amazon Linux and Node.js 14.


5. Save the project.



6. Set Up CodeDeploy

1. Go to CodeDeploy and create a new deployment application.


2. Choose EC2/On-Premises as the compute platform.


3. Set up a deployment group for your EC2 instance (ensure the instance has the CodeDeploy agent installed and the correct IAM role).



7. Set Up CodePipeline

1. Go to CodePipeline and create a new pipeline.


2. Select CodeCommit as the source, choose the repository and branch.


3. Add CodeBuild as the build provider.


4. Add CodeDeploy as the deploy provider, and select your application and deployment group.


5. Create the pipeline.



8. Testing the Pipeline

After committing and pushing changes to your CodeCommit repository, CodePipeline will automatically trigger the build, and upon successful build completion, CodeDeploy will deploy the application to your EC2 instance.

Let me know if you'd like to customize any part of the process!

