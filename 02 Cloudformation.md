Here's a guide to launch infrastructure using AWS CloudFormation, utilizing advanced features like cfn-init for configuring EC2 instances and cfn-signal for notifying CloudFormation when instance setup is complete.

1. CloudFormation Template with EC2 Instance

The CloudFormation template below provisions an EC2 instance, installs and configures a simple web server using cfn-init, and signals CloudFormation using cfn-signal when the instance is ready.

CloudFormation Template (YAML)
```
AWSTemplateFormatVersion: "2010-09-09"
Description: CloudFormation Stack with cfn-init and cfn-signal

Parameters:
  InstanceType:
    Description: EC2 instance type
    Type: String
    Default: t2.micro
    AllowedValues: 
      - t2.micro
      - t2.small
      - t2.medium
    ConstraintDescription: Must be a valid EC2 instance type.

  KeyName:
    Description: Name of an existing EC2 KeyPair to enable SSH access
    Type: AWS::EC2::KeyPair::KeyName

Resources:
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Metadata:
      AWS::CloudFormation::Init:
        config:
          packages:
            yum:
              httpd: []
          files:
            /var/www/html/index.html:
              content: |
                <html>
                  <head><title>Hello World</title></head>
                  <body><h1>Hello from EC2!</h1></body>
                </html>
              mode: '000644'
              owner: root
              group: root
          services:
            sysvinit:
              httpd:
                enabled: true
                ensureRunning: true
                files:
                  - /var/www/html/index.html
    Properties:
      InstanceType: !Ref InstanceType
      KeyName: !Ref KeyName
      ImageId: ami-0c55b159cbfafe1f0  # Amazon Linux 2 AMI
      UserData:
        Fn::Base64: !Sub |
          #!/bin/bash
          yum update -y
          yum install -y aws-cfn-bootstrap
          /opt/aws/bin/cfn-init -v --stack ${AWS::StackName} --resource MyEC2Instance --region ${AWS::Region}
          /opt/aws/bin/cfn-signal -e $? --stack ${AWS::StackName} --resource MyEC2Instance --region ${AWS::Region}

  WebServerSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties: 
      GroupDescription: Enable HTTP access
      SecurityGroupIngress: 
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0

Outputs:
  WebsiteURL:
    Description: The URL of the website
    Value: !Sub "http://${MyEC2Instance.PublicIp}"
```
2. Explanation of Template

Parameters:

InstanceType allows you to choose the EC2 instance type.

KeyName lets you specify an existing EC2 KeyPair for SSH access.


Resources:

EC2 Instance (MyEC2Instance):

Metadata section uses cfn-init to configure the instance:

Install the Apache web server (httpd).

Create a sample HTML page in /var/www/html/index.html.

Ensure the Apache service is enabled and running.


User data section:

Runs cfn-init to trigger the configuration specified in Metadata.

Uses cfn-signal to signal CloudFormation that the instance is ready, allowing CloudFormation to know if the configuration process was successful.




Security Group (WebServerSecurityGroup):

Allows inbound HTTP (port 80) and SSH (port 22) traffic.


Outputs:

WebsiteURL: Provides the public URL of the web server.



3. How cfn-init and cfn-signal Work

cfn-init:

This command runs during the EC2 instance setup to execute the configurations specified in the CloudFormation template’s Metadata section.

It installs packages, creates files, and starts services.


cfn-signal:

This command signals CloudFormation to indicate whether the instance setup was successful (0 exit code) or failed (non-zero exit code).

It's essential for stacks where the success of the instance configuration determines if CloudFormation should continue with the rest of the stack creation.



4. Deploy the Stack

1. Upload the Template:

Save the template as ec2-webserver.yaml.

Go to the AWS CloudFormation console.

Click Create stack and upload the ec2-webserver.yaml file.



2. Fill in Parameters:

Select your key pair (for SSH access).

Choose the instance type (t2.micro by default).



3. Launch the Stack.



5. Verify the Deployment

After the stack is successfully created, CloudFormation will output the WebsiteURL. You can open the link in your browser to see the Hello from EC2! message, confirming that the instance is properly configured and serving content.

6. Summary of Advanced Features

cfn-init automates the installation and configuration of software during EC2 instance launch.

cfn-signal ensures CloudFormation knows when an EC2 instance is ready, allowing for more reliable stack creation and preventing premature stack completion.


This approach is particularly useful for creating complex infrastructure setups where automated configuration management is needed.

