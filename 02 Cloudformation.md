1. Create keypair key.pem from AWS Console

2. Create a file on your machine or on git/github | template.yaml
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
      ImageId: ami-0ebfd941bbafe70c6  # Amazon Linux 2 AMI N.Virginia
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
      GroupDescription: Enable SSH, HTTP, HTTPS access
      SecurityGroupIngress: 
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0
        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIp: 0.0.0.0/0  
```

3. AWS Cloudformation
4. Choose Existing template | upload a template file
5. Add Stack name
6. Check instance public-ip in browser
7. Delete Stack
8. Delete S3 Bucket
