Guide for demonstrating the use of GitHub, AWS CodeBuild, and AWS CodeDeploy on an EC2 instance with a sample application. We’ll create a simple Node.js web application, store it in a GitHub repository, and automate its deployment to an EC2 instance using CodeBuild and CodeDeploy.

### Step 1: Create a GitHub Repository
1. **Create a new repository** on GitHub and initialize it with a `README.md` file.
2. **Clone the repository** to your local machine:
   ```bash
   git clone https://github.com/atulkamble/webapp
   cd webapp
   ```

### Step 2: Create a Sample Node.js Application
1. Initialize a new Node.js project:
   ```bash
   npm init -y
   ```
2. Install the `express` module:
   ```bash
   npm install express
   ```
3. Create a simple `app.js` file:
   ```javascript
   const express = require('express');
   const app = express();
   const port = 3000;

   app.get('/', (req, res) => {
     res.send('Hello, World!');
   });

   app.listen(port, () => {
     console.log(`App is running on http://localhost:${port}`);
   });
   ```

4. Add a `Procfile` (for deployment):
   ```
   web: node app.js
   ```

5. Commit your changes and push them to GitHub:
   Note: Configure Developer Setting | Create Token | Commit Code to repo
   ```bash
   git add .
   git commit -m "added code"
   git push origin main
   ```

### Step 3: Set Up an EC2 Instance
1. **Launch an EC2 instance** (Amazon Linux 2). webapp | webapp.key | t2.micro | ssh
2. Update SG: 3000 in Inbound
3. Connect to your instance via SSH and install the required dependencies:
   ```bash
   sudo yum update -y
   sudo yum install git -y
   git config --global user.name "Atul Kamble"
   git config --global user.email "atul_kamble@hotmail.com"
   sudo yum install nodejs -y
   npm install --save-dev mocha
   ```

4. Install the CodeDeploy Agent on EC2:
   ```bash
   sudo yum update -y
   sudo yum install ruby -y
   sudo yum install wget -y
   cd /home/ec2-user
   sudo wget https://aws-codedeploy-us-east-2.s3.us-east-2.amazonaws.com/latest/install
   sudo chmod +x ./install
   sudo ./install auto
   sudo service codedeploy-agent start
   sudo service codedeploy-agent status
   ```

### Step 4: Set Up AWS CodeDeploy
1. In the **AWS Management Console**, go to **CodeDeploy** and create a new **application** and **deployment group**.
   Developer Tools>>CodeDeploy>>Applications>>Create application
   Name it | webapp | Application type: EC2/On-premises.

2. Deployment group: Create a new one with EC2 as the compute platform and select the EC2 instance.
   Developer Tools>>CodeDeploy>>Applications>>webapp>>Create deployment group
   Name it | webapp-group | Select Configurations
   
4. IAM >> Create Role >> Name it | CodeDeployServiceRole
   
5. **Install the CodeDeploy agent** on your EC2 instance if you haven’t done so already.

### Step 5: Create `appspec.yml` for CodeDeploy
In the root of your GitHub repo, add an `appspec.yml` file to define how CodeDeploy will handle the deployment:
```yaml
version: 0.0
os: linux
files:
  - source: /
    destination: /home/ec2-user/node-app

hooks:
  AfterInstall:
    - location: scripts/install_dependencies.sh
      timeout: 300
      runas: ec2-user
  ApplicationStart:
    - location: scripts/start_server.sh
      timeout: 300
      runas: ec2-user
```

### Step 6: Add Deployment Scripts
In your GitHub repo, create a `scripts` folder with the following two files:

- **`install_dependencies.sh`**:
  ```bash
  #!/bin/bash
  cd /home/ec2-user/node-app
  npm install
  ```

- **`start_server.sh`**:
  ```bash
  #!/bin/bash
  cd /home/ec2-user/node-app
  node app.js > app.log 2>&1 &
  ```

Commit and push these changes to GitHub:
```bash
git add .
git commit -m "Added appspec.yml and deployment scripts"
git push origin main
```

### Step 7: Set Up AWS CodeBuild
1. In the **AWS Management Console**, go to **CodeBuild** and create a new build project.
2. Developer Tools>>CodeBuild>>Build projects>>Create build project
3. **Source**: Connect it to your GitHub repository.
4. **Buildspec**: Either create a `buildspec.yml` file in your repo or define the build commands directly in CodeBuild.
   ```yaml
   version: 0.2

   phases:
     install:
       commands:
         - echo Installing Node.js
         - npm install

     build:
       commands:
         - echo Build started on `date`
         - npm test
   ```

### Step 8: Automate the Deployment
1. Set up **CodePipeline** to automate the deployment from GitHub to EC2 using CodeBuild and CodeDeploy.
2. Create a pipeline:
   - Source: GitHub repository.
   - Build: CodeBuild.
   - Deploy: CodeDeploy.

### Step 9: Test the Deployment
1. Push a change to your GitHub repository (e.g., modify the `app.js` file).
2. This should trigger CodePipeline to build your application using CodeBuild and deploy it to your EC2 instance via CodeDeploy.

3. Verify the deployment by visiting your EC2 instance's public IP on port 3000:
   ```
   http://your-ec2-public-ip:3000
   http://184.72.68.27:3000/
   ```

This setup demonstrates the integration of GitHub, CodeBuild, and CodeDeploy for automatic deployment on an EC2 instance.
