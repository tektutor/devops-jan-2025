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


