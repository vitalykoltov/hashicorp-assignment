# Deploying Containerized Docker NGINX webserver with Terraform.

[Terraform](https://en.wikipedia.org/wiki/Terraform_(software)) is an open-source Infrastructure as code (IaC) software created by [HashiCorp](https://www.hashicorp.com/).  
- After completing this tutorial you will learn how to provision the latest NGINX webserver using Terraform and Docker on a supported platform. Note, we will be using a Debian Linux patform in this tutorial.
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
- Confirm the Installation was successful by running:

```
$ terraform -v
Terraform v1.2.9
on linux_amd64
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

## Create a directory for Terraform named terraform-demo

```
$mkdir terraform-demo
```
## Create Terraform configuration file main.cf in the newly created terraform-demo directory
```
$cd terraform-demo
$touch main.cf
```

### Add the following configuration to the Terraform configuration fileusing an editor of your choice:
```
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
- If successfully run, you will see an output similar to this:
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

## Provision the NGINX server in a Docker container using the following command: 

```
$terraform apply
```
When prompted to continue due to warning for using deprecated attribute "latest", type "yes":

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

## Verify the Docker container is running with command: docker ps. Running Docker container will loo similar to this output:

```
$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS                NAMES
e1e364ac671b   2d389e545974   "/docker-entrypoint.…"   14 seconds ago   Up 12 seconds   0.0.0.0:80->80/tcp   training
```

## Verify connectivity to the NGINX webserver running in the Docker container we created using "curl http://localhost" command.  You should see similar output to this:

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
## Deprovision NGINX webserver and delete Docker container

- Run the following command: `terraform destroy`:
- When prompted for a confirmation message, type `yes` and press ENTER.
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
In this tutorial you have learned how to use Terraform with Docker to create and manage container-based webservers.  Next Steps:

[Creating Infrastructure on AWS using Terraform](https://learn.hashicorp.com/tutorials/terraform/aws-build?in=terraform/aws-get-started)
