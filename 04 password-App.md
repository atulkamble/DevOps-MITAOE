Here’s a step-by-step guide to implement a basic password-saving application using AWS Systems Manager (SSM) Parameter Store in a Node.js environment. This application will securely store and retrieve passwords using the Parameter Store.

1. Setting up AWS Systems Manager Parameter Store

Step 1: Add a password to Parameter Store

1. Go to the AWS Systems Manager Console.


2. In the left-hand navigation pane, click Parameter Store under Application Management.


3. Click Create Parameter.


4. Specify the following:

Name: /passwords/myAppPassword (You can use any naming convention that suits your needs).

Tier: Standard.

Type: SecureString (to store the password encrypted using KMS).

KMS Key ID: (Leave this as the default unless you have a specific KMS key you want to use).

Value: Add your password, e.g., SuperSecurePassword123.



5. Click Create Parameter.



2. Implement a Node.js Application to Store and Retrieve Passwords

Project Setup

1. Initialize a Node.js project:

mkdir password-manager
cd password-manager
npm init -y


2. Install the required dependencies:

npm install aws-sdk dotenv



Environment Variables

Create a .env file to store your AWS region and any other environment configurations:

AWS_REGION=your-region

Replace your-region with the AWS region you are using, e.g., us-east-1.

Node.js Script to Retrieve Password from Parameter Store

Create a passwordManager.js file with the following code to retrieve and display a stored password.

require('dotenv').config();
const AWS = require('aws-sdk');

// Configure AWS SDK to use the correct region
const ssm = new AWS.SSM({ region: process.env.AWS_REGION });

/**
 * Function to get the password from AWS Parameter Store
 * @param {string} paramName - The name of the parameter to retrieve
 */
async function getPassword(paramName) {
  const params = {
    Name: paramName, // Name of the parameter
    WithDecryption: true, // Decrypt the parameter if it is a SecureString
  };

  try {
    const data = await ssm.getParameter(params).promise();
    console.log('Retrieved password:', data.Parameter.Value);
    return data.Parameter.Value;
  } catch (error) {
    console.error('Error retrieving parameter:', error);
  }
}

// Example usage
const paramName = '/passwords/myAppPassword'; // Update with your parameter name
getPassword(paramName);

3. Configure IAM Role for EC2 or Lambda

To allow your application to access the Parameter Store, you’ll need to assign the correct IAM role to your EC2 instance or Lambda function, whichever you’re using to run the application.

For EC2 Instance:

1. Attach an IAM role to your EC2 instance with the following managed policy:

AmazonSSMReadOnlyAccess (This policy allows read-only access to the Parameter Store).



2. You can do this by:

Going to the EC2 console.

Select your instance, click Actions > Security > Modify IAM role.

Attach the role with AmazonSSMReadOnlyAccess permission.




For Lambda:

1. Attach an IAM role to your Lambda function with the AmazonSSMReadOnlyAccess policy.


2. This role can be attached while creating the Lambda function or from the IAM console under the Lambda's permissions.



4. Running the Application

After setting up your environment, run the Node.js application to retrieve the password stored in Parameter Store.

node passwordManager.js

If everything is configured correctly, the application should output the password you stored in the Parameter Store:

Retrieved password: SuperSecurePassword123

5. Storing Passwords

If you want to store passwords directly from the application instead of manually creating them in the Parameter Store, you can modify the script to include the putParameter method.

Here’s how you can modify the passwordManager.js file to save a password to the Parameter Store:

/**
 * Function to store a password in AWS Parameter Store
 * @param {string} paramName - The name of the parameter to store
 * @param {string} paramValue - The value of the parameter (password)
 */
async function storePassword(paramName, paramValue) {
  const params = {
    Name: paramName, // Name of the parameter
    Value: paramValue, // The password to store
    Type: 'SecureString', // Store it as a SecureString
    Overwrite: true, // Overwrite if it already exists
  };

  try {
    await ssm.putParameter(params).promise();
    console.log('Password stored successfully.');
  } catch (error) {
    console.error('Error storing parameter:', error);
  }
}

// Example usage to store a password
const newPassword = 'AnotherSecurePassword123'; // Replace with your password
storePassword(paramName, newPassword);

You can run this to store a password in the Parameter Store.

node passwordManager.js

6. Security Considerations

IAM Roles: Make sure to limit access to the Parameter Store with least privilege principles. Only assign AmazonSSMReadOnlyAccess or create a custom policy that limits access to specific parameters.

KMS Encryption: When storing secrets as SecureString, they are encrypted using AWS KMS by default. You can specify your own KMS keys if necessary.

Environment Variables: Use .env files or secrets management solutions to store sensitive environment information like AWS credentials or region configurations.


Summary

With this approach, you've created a basic password-saving application using AWS Systems Manager Parameter Store. The application retrieves and optionally stores passwords using the AWS SDK for JavaScript and securely stores sensitive data using the SecureString type. You can further expand this by adding user authentication or an API to interact with the password management features.

