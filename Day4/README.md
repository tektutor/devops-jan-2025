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

Expected output
![image](https://github.com/user-attachments/assets/0bb2f24b-307d-429e-aa03-d6d3c9705269)
![image](https://github.com/user-attachments/assets/63907201-a0e1-4cbc-9d2c-d88b9b65a528)


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

