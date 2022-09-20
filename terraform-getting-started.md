# Deploying Containerized Docker NGINX webserver with Terraform on Linux Servers.

[Terraform](https://en.wikipedia.org/wiki/Terraform_(software)) is an open-source Infrastructure as code (IaC) software created by [HashiCorp](https://www.hashicorp.com/).  
- After completing this tutorial you will learn how to provision the latest NGINX webserver using Terraform and Docker on a supported Linux platform. 
- Estimated time to complete: 60 minutes.
- In the following tasks we will: 
- Install Terraform package for your operating system
- Install Docker Engine (Docker Desktop for Linux)
- Create Terraform configuration
- Initialize Terraform configuration with a Docker plugin
- Provision Docker container with the latest NGINX server release
- Connect to the newly provisioned NGINX server
- Destroy Docker container running NGINX server

## Prerequisites

- A Linux server with access to the internet
- Ability to create and edit files on the linux server
- Ability to install linux packages

## Install Terraform

Install Terraform on Linux server by navigating to [Terraform.io](https://www.terraform.io/downloads.html) and selecting Linux from the distribution options.
- In this turturial we will use Ubuntu/Debian flavor of linux.

We begin by running the following commands in the linux shell:

```shell
$wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg]
$echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
$sudo apt update && sudo apt install terraform
```

## Install Docker Engine

- [Install Docker Engine](https://docs.docker.com/engine/install/) for the Docker Desktop for Linux Platform
- We will be installing on the Debian Linux server
- [Install Docker Desktop on Debian](https://docs.docker.com/desktop/install/debian/)
- Confirm Docker Engine has been installed by running the following command:

```
$docker info
```
- You will see output similar to this if your environment has been installed:
```
Client:
 Context:    default
 Debug Mode: false
 Plugins:
  app: Docker App (Docker Inc., v0.9.1-beta3)
  buildx: Docker Buildx (Docker Inc., v0.9.1-docker)
  scan: Docker Scan (Docker Inc., v0.17.0)

Server:
 Containers: 0
  Running: 0
  Paused: 0
  Stopped: 0
 Images: 0
 Server Version: 20.10.18
 Storage Driver: overlay2
  Backing Filesystem: extfs
  Supports d_type: true
  Native Overlay Diff: true
  userxattr: false
 Logging Driver: json-file
 Cgroup Driver: cgroupfs
 Cgroup Version: 1
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
 Swarm: inactive
 Runtimes: io.containerd.runc.v2 io.containerd.runtime.v1.linux runc
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: 9cd3357b7fd7218e4aec3eae239db1f68a5a6ec6
 runc version: v1.1.4-0-g5fd4c4d
 init version: de40ad0
 Security Options:
  apparmor
  seccomp
   Profile: default
 Kernel Version: 5.10.133+
 Operating System: Debian GNU/Linux 11 (bullseye) (containerized)
 OSType: linux
 Architecture: x86_64
 CPUs: 4
 Total Memory: 15.63GiB
 Name: cs-788453672897-default
 ID: UPN7:BYDH:6D3H:ZCXO:R23H:ZTSB:PV3R:36ZT:ROBV:MKN5:FCKZ:VMFP
 Docker Root Dir: /var/lib/docker
 Debug Mode: false
 Registry: https://index.docker.io/v1/
 Labels:
 Experimental: false
 Insecure Registries:
  127.0.0.0/8
 Registry Mirrors:
  https://us-mirror.gcr.io/
 Live Restore Enabled: false
 ```
 
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
