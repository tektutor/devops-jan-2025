# Day 4

## Installing Terraform in Ubuntu
```
sudo apt-get update && sudo apt-get install -y gnupg software-properties-common

wget -O- https://apt.releases.hashicorp.com/gpg | \
gpg --dearmor | \
sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null

gpg --no-default-keyring \
--keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg \
--fingerprint

echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
sudo tee /etc/apt/sources.list.d/hashicorp.list

sudo apt update
sudo apt-get install terraform
terraform -install-autocomplete
```

Checking terraform installation
```
terraform -version
```

## Lab - Write your first terraform script

We need to create a folder to keep the terraform scripts
```
cd ~
mkdir -p create-docker-container
cd create-docker-container
```

Create a file name main.tf with the below content
```
terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 3.0.1"
    }
  }
}

provider "docker" {}

resource "docker_image" "nginx" {
  name         = "nginx"
  keep_locally = false
}

resource "docker_container" "nginx" {
  image = docker_image.nginx.image_id
  name  = "tutorial"

  ports {
    internal = 80
    external = 8000
  }
}
```

Next, we need to download the terraform docker_image provider 
```
terraform init
```

Next, we can run the terraform script
```
terraform apply
```

Now check if the containers are created
```
docker ps
```

Once you are done with the lab exercise, you can cleanup/discard the resources created by Terraform 
```
terraform destroy
```

Expected output
![image](https://github.com/user-attachments/assets/f0a706d1-b4cf-495f-ac7d-d338bbacffea)
![image](https://github.com/user-attachments/assets/fd61166a-c26a-4c03-bcbe-18709b0f7cb9)
![image](https://github.com/user-attachments/assets/0e16a024-c8e5-4740-8183-a42158945589)
![image](https://github.com/user-attachments/assets/328f9615-2369-4cc1-9506-ea902d73d7b3)
![image](https://github.com/user-attachments/assets/334c2a60-d910-44bd-a602-bf2921b6928c)

## Lab - Provisioning AWS ec2 instance via Terraform

We need to first export the AWS Access key and respective secret key
```
export AWS_ACCESS_KEY_ID="your-aws-access-key"
export AWS_SECRET_ACCESS_KEY="your-aws-secret-key"
export AWS_REGION="ap-south-1"
```
Then create a folder 
```
mkdir -p ~/create-ec2-instance
```

Create a file name main.tf with the below code
```
provider "aws" {
}

data "aws_ami" "ubuntu" {
  most_recent = true

  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-focal-20.04-amd64-server-*"]
  }

  filter {
    name   = "virtualization-type"
    values = ["hvm"]
  }

  owners = ["099720109477"] # Canonical
}

resource "aws_security_group" "sg_8080" {
  name = "terraform-learn-state-sg-8080"
  ingress {
    from_port   = "8080"
    to_port     = "8080"
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  // connectivity to ubuntu mirrors is required to run `apt-get update` and `apt-get install apache2`
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

resource "aws_instance" "example" {
  ami                    = data.aws_ami.ubuntu.id
  instance_type          = "t2.micro"
  vpc_security_group_ids = [aws_security_group.sg_8080.id]
  user_data              = <<-EOF
              #!/bin/bash
              apt-get update
              apt-get install -y apache2
              sed -i -e 's/80/8080/' /etc/apache2/ports.conf
              echo "Hello World" > /var/www/html/index.html
              systemctl restart apache2
              EOF
  tags = {
    Name = "terraform-learn-state-ec2"
  }
}
```

Let's run the terraform script
```
terraform init
terraform apply 
```

Expected output
![image](https://github.com/user-attachments/assets/2ae184a1-668f-4e88-83a4-7198082b1c74)
