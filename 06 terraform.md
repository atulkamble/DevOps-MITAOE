// ports
```
22 - ssh - secure shell
3389 - rdp - remote desktop 
80 - http - 
443 - https - 
```

1)   Create User and User Account
```
IAM >> create user >> atulkamble
>> create user group >> admin
>> security | credentials >> username,password >> download .csv file 
>> security | create access key, secret access key >> download .csv file 

region = us-east-1
```
2) Lanuch instance - connect - ssh
example:
``` 
ssh -i "terraformkey.pem" ec2-user@ec2-54-90-122-220.compute-1.amazonaws.com
```
3) install awscli
```
sudo yum install awscli -y
aws --version
aws configure
```
```
>> enter access key, secret access key,
>> region-name example:us-east-1
>> output example:json
```
4) install terraform
```
sudo yum install -y yum-utils shadow-utils
sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/AmazonLinux/hashicorp.repo
sudo yum -y install terraform
terraform --version
```
```
mkdir project
cd project
touch main.tf
sudo nano main.tf
```
// copy code
```
terraform {
  required_providers {
    aws = {
      source = "hashicorp/aws"
      version = "5.66.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "webserver" {
  ami           = "ami-0182f373e66f89c85"
  instance_type = "t2.micro"

  tags = {
    Name = "MyFirstTerraformInstance"
  }
}
```
5) run and apply script
```
terraform init
terraform plan
terraform apply
terraform destroy
```
6)
>> Crosscheck AWS Account 




