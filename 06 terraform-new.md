Here's a step-by-step guide to launching AWS infrastructure using Terraform, focusing on setting up an EC2 instance and a VPC as an example.

1. Install Terraform

1. Download and install Terraform from the official website: Terraform Downloads.


2. Verify the installation:

terraform -v



2. Set Up Your AWS CLI Credentials

1. Install and configure AWS CLI:

aws configure

Provide your AWS Access Key ID, Secret Access Key, Region, and Output format.



3. Create Terraform Configuration Files

Step 1: Create a Working Directory

Create a directory for your Terraform project:

mkdir terraform-aws-infrastructure
cd terraform-aws-infrastructure

Step 2: Create the Terraform Files

1. main.tf – This file contains the core infrastructure code.


2. variables.tf – This file defines the variables.


3. outputs.tf – This file contains outputs of the deployed resources.



Example Infrastructure Code

main.tf

This file defines the AWS provider, creates a VPC, an EC2 instance, and necessary resources.

provider "aws" {
  region = var.aws_region
}

# Create a VPC
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
  tags = {
    Name = "MyVPC"
  }
}

# Create a subnet
resource "aws_subnet" "main" {
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.1.0/24"
  availability_zone = "us-east-1a"
  tags = {
    Name = "MySubnet"
  }
}

# Create an Internet Gateway
resource "aws_internet_gateway" "main" {
  vpc_id = aws_vpc.main.id
  tags = {
    Name = "MyIGW"
  }
}

# Create a Route Table
resource "aws_route_table" "main" {
  vpc_id = aws_vpc.main.id
  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.main.id
  }
  tags = {
    Name = "MyRouteTable"
  }
}

# Associate Route Table with Subnet
resource "aws_route_table_association" "main" {
  subnet_id      = aws_subnet.main.id
  route_table_id = aws_route_table.main.id
}

# Create a Security Group
resource "aws_security_group" "main" {
  vpc_id = aws_vpc.main.id
  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
  tags = {
    Name = "MySecurityGroup"
  }
}

# Create an EC2 instance
resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0" # Amazon Linux 2 AMI
  instance_type = "t2.micro"
  subnet_id     = aws_subnet.main.id
  security_groups = [aws_security_group.main.name]

  tags = {
    Name = "MyEC2Instance"
  }

  user_data = <<-EOF
              #!/bin/bash
              echo "Hello, World!" > index.html
              nohup python3 -m http.server 80 &
              EOF
}

variables.tf

This file defines variables that make the Terraform configuration more flexible.

variable "aws_region" {
  default = "us-east-1"
}

outputs.tf

This file provides outputs from the infrastructure deployment (e.g., the public IP of the EC2 instance).

output "instance_public_ip" {
  value = aws_instance.web.public_ip
}

output "vpc_id" {
  value = aws_vpc.main.id
}

4. Initialize, Plan, and Apply Terraform

Step 1: Initialize the Terraform Working Directory

Before running Terraform, you need to initialize the project to download the necessary providers.

terraform init

Step 2: Review the Terraform Plan

The plan command shows you what changes will be made before actually applying them.

terraform plan

This command will output a detailed description of the infrastructure that will be created.

Step 3: Apply the Terraform Configuration

Apply the configuration to create the AWS resources.

terraform apply

You will be prompted to confirm the action by typing yes. After that, Terraform will start creating the resources.

Step 4: Check the Outputs

Once Terraform completes, it will display any outputs defined in the outputs.tf file, such as the EC2 instance's public IP.

5. Verify Infrastructure in AWS Console

Go to the EC2 Console to verify the EC2 instance is running.

Go to the VPC Console to verify the VPC, Subnets, and Internet Gateway are created.


6. Destroy the Infrastructure

When you're done with the infrastructure, you can destroy everything using the terraform destroy command.

terraform destroy

This command will delete all resources that were created by your Terraform configuration.

Summary

Terraform allows you to define, deploy, and manage AWS infrastructure as code.

The main.tf file defines the infrastructure resources, while variables.tf and outputs.tf make the configuration flexible and reusable.

You can use terraform init, plan, apply, and destroy to manage your AWS resources in a controlled way.


This setup demonstrates launching a simple EC2 instance and networking resources. You can expand it by adding more resources such as RDS, S3, or Lambda.

