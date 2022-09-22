# Getting started with Terraform on Docker: NGINX web server deployment (60 minutes).

[Terraform](https://en.wikipedia.org/wiki/Terraform_(software)) is an open-source Infrastructure as code (IaC) software created by [HashiCorp](https://www.hashicorp.com/).  
[Docker](https://en.wikipedia.org/wiki/Docker_(software)) is a Platform-as-a-service (Paas) containerization software. 

In this tutorial you will learn how to provision the latest NGINX web server using Terraform and Docker on a supported platform. 
```
Note: We are using a Debian Linux patform in this tutorial.

```
In the next 60 minutes you will:

[Install Terraform package for your operating system](#install-terraform)

[Install Docker Engine](#install-docker-engine)

[Create Terraform configuration](#create-terraform-configuration)

[Provision Docker container with the latest NGINX web server release](#provision-the-nginx-web-server-in-a-docker-container)

[Connect to NGINX web server](#connect-to-nginx-web-server)

[Destroy Docker container](#destroy-docker-container)

## Prerequisites

- [Supported](https://www.terraform.io/downloads) system with Internet access
- Ability to create and edit files on the system
- Ability to install Terraform and [Docker software](https://docs.docker.com/engine/install/) on the system

## Install Terraform

Install Terraform by navigating to [Terraform.io](https://www.terraform.io/downloads.html) and selecting Linux from the distribution options.

Select the platform choice appropriate for your environment. The following steps are specific to Debian linux system: 

```shell
$wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg]
$echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
$sudo apt update && sudo apt install terraform
```
Confirm Installation was successful. Type `terraform -v` and press ENTER:

```
$ terraform -v
Terraform v1.2.9
on linux_amd64
```
## Install Docker Engine

[Install Docker Engine for Debian Linux](https://docs.docker.com/desktop/install/debian/). If you are using a different Operating System, choose the corresponding installation [package](https://docs.docker.com/engine/install/) and follow instructions from Docker on how to install it.

Confirm Docker Engine has been installed. Type `docker version` and press ENTER:

```
$docker version
```

Successfull installation returns output similar to this:

```
Client: Docker Engine - Community
 Version:           20.10.18
 API version:       1.41
 Go version:        go1.18.6
 Git commit:        b40c2f6
 Built:             Thu Sep  8 23:12:08 2022
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.18
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.18.6
  Git commit:       e42327a
  Built:            Thu Sep  8 23:09:59 2022
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.6.8
  GitCommit:        9cd3357b7fd7218e4aec3eae239db1f68a5a6ec6
 runc:
  Version:          1.1.4
  GitCommit:        v1.1.4-0-g5fd4c4d
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
```


## Create Terraform Configuration

Create a directory for Terraform named *terraform-demo*

```shell
$mkdir terraform-demo
```
Create Terraform configuration file `main.cf` in the newly created *terraform-demo* directory

```shell
$cd terraform-demo
$touch main.cf
```

Add the following configuration to the Terraform configuration file using an editor of your choice:
```text
terraform {
  required_providers {
    docker = {
      source = "kreuzwerker/docker"
    }
  }
}
terraform {
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

Initialize Terraform project and download plugin for Docker to enable Docker image provisioning:
```
$terraform init
```
Successfully initialization contains output with the lines *Terraform has been successfully installed!*:
```

Initializing the backend...

Initializing provider plugins...
- Finding latest version of kreuzwerker/docker...
- Installing kreuzwerker/docker v2.21.0...
- Installed kreuzwerker/docker v2.21.0 (self-signed, key ID BD080C4571C6104C)

Partner and community providers are signed by their developers.
If you'd like to know more about provider signing, you can read about it here:
https://www.terraform.io/docs/cli/plugins/signing.html

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

## Provision the NGINX web server in a Docker container 

Provision the NGINX web server in a Docker containerusing. Type `terraform apply` and press ENTER:

```
$terraform apply
```
When prompted, type `yes`. Terraform will provision the latest version of an NGNIX web server running in a Docker container.

```

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # docker_container.nginx will be created
  + resource "docker_container" "nginx" {
      + attach           = false
      + bridge           = (known after apply)
      + command          = (known after apply)
      + container_logs   = (known after apply)
      + entrypoint       = (known after apply)
      + env              = (known after apply)
      + exit_code        = (known after apply)
      + gateway          = (known after apply)
      + hostname         = (known after apply)
      + id               = (known after apply)
      + image            = (known after apply)
      + init             = (known after apply)
      + ip_address       = (known after apply)
      + ip_prefix_length = (known after apply)
      + ipc_mode         = (known after apply)
      + log_driver       = (known after apply)
      + logs             = false
      + must_run         = true
      + name             = "training"
      + network_data     = (known after apply)
      + read_only        = false
      + remove_volumes   = true
      + restart          = "no"
      + rm               = false
      + runtime          = (known after apply)
      + security_opts    = (known after apply)
      + shm_size         = (known after apply)
      + start            = true
      + stdin_open       = false
      + stop_signal      = (known after apply)
      + stop_timeout     = (known after apply)
      + tty              = false

      + healthcheck {
          + interval     = (known after apply)
          + retries      = (known after apply)
          + start_period = (known after apply)
          + test         = (known after apply)
          + timeout      = (known after apply)
        }

      + labels {
          + label = (known after apply)
          + value = (known after apply)
        }

      + ports {
          + external = 80
          + internal = 80
          + ip       = "0.0.0.0"
          + protocol = "tcp"
        }
    }

  # docker_image.nginx will be created
  + resource "docker_image" "nginx" {
      + id          = (known after apply)
      + image_id    = (known after apply)
      + latest      = (known after apply)
      + name        = "nginx:latest"
      + output      = (known after apply)
      + repo_digest = (known after apply)
    }

Plan: 2 to add, 0 to change, 0 to destroy.
╷
│ Warning: Deprecated attribute
│
│   on main.tf line 12, in resource "docker_container" "nginx":
│   12:   image = docker_image.nginx.latest
│
│ The attribute "latest" is deprecated. Refer to the provider documentation for details.
│
│ (and one more similar warning elsewhere)
╵

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

docker_image.nginx: Creating...
docker_image.nginx: Creation complete after 3s [id=sha256:2d389e545974d4a93ebdef09b650753a55f72d1ab4518d17a30c0e1b3e297444nginx:latest]
docker_container.nginx: Creating...
docker_container.nginx: Creation complete after 1s [id=e5f577b7684e15f7347fbead0c7f0d9ef7f1db4db759c71db51450450224f881]
╷
│ Warning: Deprecated attribute
│
│   on main.tf line 12, in resource "docker_container" "nginx":
│   12:   image = docker_image.nginx.latest
│
│ The attribute "latest" is deprecated. Refer to the provider documentation for details.
│
│ (and one more similar warning elsewhere)
╵

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

Verify Docker container is running. Type `docker ps` and press ENTER. 

Inspect information returned.  Notice *Container ID, Image and Ports* details:

```
$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS                NAMES
e1e364ac671b   2d389e545974   "/docker-entrypoint.…"   14 seconds ago   Up 12 seconds   0.0.0.0:80->80/tcp   training
```

## Connect to NGINX web server. 

Connect to NGINX web server running inside Docker container.  Type `curl http://localhost` and press ENTER: 

Output of a successful connection request will include a string **Welcome to nginx!**:

```
$ curl http://localhost
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
## Destroy Docker container

Destroy Docker container with NGINX web server.  Type `terraform destroy` and press ENTER:

When prompted, type `yes` and press ENTER.

```
$ terraform destroy
docker_image.nginx: Refreshing state... [id=sha256:2d389e545974d4a93ebdef09b650753a55f72d1ab4518d17a30c0e1b3e297444nginx:latest]
docker_container.nginx: Refreshing state... [id=e5f577b7684e15f7347fbead0c7f0d9ef7f1db4db759c71db51450450224f881]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # docker_container.nginx will be destroyed
  - resource "docker_container" "nginx" {
      - attach            = false -> null
      - command           = [
          - "nginx",
          - "-g",
          - "daemon off;",
        ] -> null
      - cpu_shares        = 0 -> null
      - dns               = [] -> null
      - dns_opts          = [] -> null
      - dns_search        = [] -> null
      - entrypoint        = [
          - "/docker-entrypoint.sh",
        ] -> null
      - env               = [] -> null
      - gateway           = "172.18.0.1" -> null
      - group_add         = [] -> null
      - hostname          = "e5f577b7684e" -> null
      - id                = "e5f577b7684e15f7347fbead0c7f0d9ef7f1db4db759c71db51450450224f881" -> null
      - image             = "sha256:2d389e545974d4a93ebdef09b650753a55f72d1ab4518d17a30c0e1b3e297444" -> null
      - init              = false -> null
      - ip_address        = "172.18.0.2" -> null
      - ip_prefix_length  = 16 -> null
      - ipc_mode          = "private" -> null
      - links             = [] -> null
      - log_driver        = "json-file" -> null
      - log_opts          = {} -> null
      - logs              = false -> null
      - max_retry_count   = 0 -> null
      - memory            = 0 -> null
      - memory_swap       = 0 -> null
      - must_run          = true -> null
      - name              = "training" -> null
      - network_data      = [
          - {
              - gateway                   = "172.18.0.1"
              - global_ipv6_address       = ""
              - global_ipv6_prefix_length = 0
              - ip_address                = "172.18.0.2"
              - ip_prefix_length          = 16
              - ipv6_gateway              = ""
              - network_name              = "bridge"
            },
        ] -> null
      - network_mode      = "default" -> null
      - privileged        = false -> null
      - publish_all_ports = false -> null
      - read_only         = false -> null
      - remove_volumes    = true -> null
      - restart           = "no" -> null
      - rm                = false -> null
      - runtime           = "runc" -> null
      - security_opts     = [] -> null
      - shm_size          = 64 -> null
      - start             = true -> null
      - stdin_open        = false -> null
      - stop_signal       = "SIGQUIT" -> null
      - stop_timeout      = 0 -> null
      - storage_opts      = {} -> null
      - sysctls           = {} -> null
      - tmpfs             = {} -> null
      - tty               = false -> null

      - ports {
          - external = 80 -> null
          - internal = 80 -> null
          - ip       = "0.0.0.0" -> null
          - protocol = "tcp" -> null
        }
    }

  # docker_image.nginx will be destroyed
  - resource "docker_image" "nginx" {
      - id          = "sha256:2d389e545974d4a93ebdef09b650753a55f72d1ab4518d17a30c0e1b3e297444nginx:latest" -> null
      - image_id    = "sha256:2d389e545974d4a93ebdef09b650753a55f72d1ab4518d17a30c0e1b3e297444" -> null
      - latest      = "sha256:2d389e545974d4a93ebdef09b650753a55f72d1ab4518d17a30c0e1b3e297444" -> null
      - name        = "nginx:latest" -> null
      - repo_digest = "nginx@sha256:0b970013351304af46f322da1263516b188318682b2ab1091862497591189ff1" -> null
    }

Plan: 0 to add, 0 to change, 2 to destroy.
╷
│ Warning: Deprecated attribute
│
│   on main.tf line 12, in resource "docker_container" "nginx":
│   12:   image = docker_image.nginx.latest
│
│ The attribute "latest" is deprecated. Refer to the provider documentation for details.
╵

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

docker_container.nginx: Destroying... [id=e5f577b7684e15f7347fbead0c7f0d9ef7f1db4db759c71db51450450224f881]
docker_container.nginx: Destruction complete after 0s
docker_image.nginx: Destroying... [id=sha256:2d389e545974d4a93ebdef09b650753a55f72d1ab4518d17a30c0e1b3e297444nginx:latest]
docker_image.nginx: Destruction complete after 0s

Destroy complete! Resources: 2 destroyed.
```

## Next steps

In this tutorial you have learned how to deploy Docker container with NGINX web server using Terraform.  You can now learn how to manage infrastructure with Terraform on other platforms such as AWS, GCP, Alibaba, Azure and OCI:

[Creating Infrastructure on AWS using Terraform](https://learn.hashicorp.com/tutorials/terraform/aws-build?in=terraform/aws-get-started)

[Build Infrastructure - Terraform GCP](https://learn.hashicorp.com/tutorials/terraform/google-cloud-platform-build)

[Build Infrsatructure - Terraform Azure](https://learn.hashicorp.com/tutorials/terraform/azure-build)

[Getting Started with Terraform on Alibaba Cloud](https://www.alibabacloud.com/blog/getting-started-with-terraform-with-alibaba-cloud-what-is-terraform_596155)

[Getting satrted with Terraform on OCI](https://docs.oracle.com/en-us/iaas/developer-tutorials/tutorials/tf-simple-infrastructure/01-summary.htm)


 ```


