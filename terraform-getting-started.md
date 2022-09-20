# Quick Start: Deploying Containerized Docker NGINX webserver with Terraform on Linux Servers.

old:Terraform is the most popular langauge for defining and provisioning infrastructure as code (IaC).
new: After completing this tutorial you will learn how to provision the latest NGINX webserver using Terraform on Linux Server.
In the following tasks we will: 
-- Install Terraform package for your operating system
-- Install Docker Engine (Docker Desktop for Linux)
-- Create Terraform configuration
-- Initialize Terraform configuration with a Docker plugin
-- Provision Docker container with the latest NGINX server release
-- Connect to the newly provisioned NGINX server
-- Destroy Docker container running NGINX server

## Prerequisites

- A Linux server with access to the internet
- Ability to create and edit files on the linux server
- Ability to install linux packages

## Install Terraform

Install Terraform on Linux server by navigating to [Terraform.io](https://www.terraform.io/downloads.html) and selecting Linux from the distribution otions.
In this turturial we will use Ubuntu/Debian flavor of linux.

We begin by running the following commands in the linux shell:

$wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg
$echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
$sudo apt update && sudo apt install terraform

## Install Docker Engine

[Install Docker Engine](https://docs.docker.com/engine/install/) for the Docker Desktop for Linux Platform
[CLI command](#cli-command) / [API call using cURL](#api-call-using-curl) / [Web UI](#web-ui)
We will be installing on the Debian Linux server
[Install Docker Desktop on Debian](https://docs.docker.com/desktop/install/debian/)


### CLI command

### API call using cURL

### Web UI

To install Terraform, simply visit [Terraform.io](https://www.terraform.io/downloads.html) and download the compressed binary application executable file deliverable for your platform, machine or environment on which you like to run code and do development.

With Terraform installed, let's dive right into it and start creating some infrastructure.

Most guys find it easiest to create a new directory on there local machine and create Terraform configuration code inside it.

```shell
$ mkdir terraform-demo
$ cd terraform-demo
```

Next, create a file for your Terraform configuration code.

```shell
$ touch main.tf
```

Paste the following lines into the file.

```hcl
terraform {
  required_providers {
    docker = {
      source = "kreuzwerker/docker"
    }
  }
}
provider "docker" {
    host = "unix:///var/run/docker.sock"
}
resource "docker_container" "nginx" {
  image = docker_image.nginx.latest
  name  = "training"
  ports {
    internal = 80
    external = 80
  }
}
resource "docker_image" "nginx" {
  name = "nginx:latest"
}
```

Initialize Terraform with the `init` command. The AWS provider will be installed. 

```shell
$ terraform init
```

You shoud check for any errors. If it ran successfully, provision the resource with the `apply` command.

```shell
$ terraform apply
```

The command will take up to a few minutes to run and will display a message indicating that the resource was created.

Finally, destroy the infrastructure.

```shell
$ terraform destroy
```

Look for a message are the bottom of the output asking for confirmation. Type `yes` and hit ENTER. Terraform will destroy the resources it had created earlier.
