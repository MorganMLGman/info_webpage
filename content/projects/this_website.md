---
title: "This website"
date: 2025-05-19T21:51:00+02:00
draft: false
author: "MorganMLG"
tags:
  - Hugo
  - Oracle
  - Cloudflare
  - GitHub
  - Github Actions
  - OpenTofu
  - Self-hosting
  - CI/CD
image: /images/projects/this_website/website.png
toc:
---
## Static Webpage

Let me explain how I created this website. Firstly, this website is a static webpage, but what is a static webpage? 

A [static webpage](https://en.wikipedia.org/wiki/Static_web_page) is a website that is not generated dynamically, but rather is pre-generated and served as-is to the user. This means that the content of the website does not change based on user input or other factors, and it is typically faster and more secure than dynamic websites. 

Ok so now you know what a static webpage is, but are there any benefits to using a static webpage? Yes, there are many benefits to using a static webpage, including:
- **Speed**: Static webpages are typically faster to load than dynamic webpages, as they do not require server-side processing or database queries.
- **Cost**: Static webpages are often cheaper to host than dynamic webpages, as they do not require a server with a database or server-side scripting capabilities.
- **Simplicity**: Static webpages are easier to create and maintain than dynamic webpages, as they do not require complex server-side programming or database management.

## Hugo

But how do you create a static webpage? There are many ways to create a static webpage, but I will explain how I created this website using [Hugo](https://gohugo.io/) and [Hugo Profile](https://github.com/gurusabarish/hugo-profile).

Hugo is a framework for building static websites quickly and easily. It is written in Go and is known for its speed and flexibility. 

Firstly, you need to install Hugo. You can do this by following the instructions on the [Hugo installation page](https://gohugo.io/getting-started/installing/). I am using Fedora, so I installed Hugo with this command: 
```bash
sudo dnf install hugo
```

 Then you can create a new Hugo site with this command:
```bash
hugo new site <site-name>
```
![Hugo new site command](/images/projects/this_website/hugo_new_site.png)


This will create a new directory with the name you specified, and it will contain the basic structure of a Hugo site. I additionally used the `--format=yaml` option to create the configuration file in YAML format. You can also use TOML or JSON, but I prefer YAML because I am more familiar with it.

## Hugo Profile

After creating the site, you need to add a theme. I used [Hugo Profile](https://github.com/gurusabarish/hugo-profile). To add the theme, you need to clone the theme repository into the `themes` directory of your Hugo site. You can do this with the following command:
```bash
git clone https://github.com/gurusabarish/hugo-profile.git themes/hugo-profile
```

After that to speed things up I copied the `config.yaml` file from the theme to the root of my site. This file contains the configuration for the Hugo and the theme itself, and it is used by Hugo to generate the site.

There are few things that you need to change in the `config.yaml` file. Firstly, you need to change the `baseURL` to the URL of your website. You can also change the `title`, `description`, and other settings to customize your site.

I don't want to go into too much detail about the configuration file, because I don't want to create tutorial about "How to create Hugo webpage". Instead, I want to explain how I created this exact website from more technical and hosting point of view.

## Hosting

After creating the site, you need to host it. There are many ways to host a static website. The easiest way is to use a [GitHub Pages](https://pages.github.com/). You can create a new repository on GitHub, push your Hugo site to the repository, and then enable GitHub Pages in the repository settings. This allows you to serve your static website directly from GitHub.

I didn't want to use GitHub Pages, because I wanted to host my website under my own domain. So I decided to use [Oracle Cloud](https://www.oracle.com/cloud/) with [Cloudflare](https://www.cloudflare.com/).

### Oracle Cloud

Oracle Cloud is a cloud computing service that offers a wide range of services, including virtual machines, storage, and networking. It is free to use for the first 30 days, and it offers a free tier with limited resources.

You can create VM on Oracle Cloud manually, using the web interface, it isn't that hard. But I wanted to automate the process, so I used [OpenTofu](https://www.opentofu.org/) to create the VM. OpenTofu is a fork of Terraform, and it allows you to create and manage cloud resources using code.

![Creating VM with Oracle Web Interface](/images/projects/this_website/oracle_vm_creation.png)


To use OpenTofu, you need to install it first. You can do this by following the instructions on the [OpenTofu installation page](https://opentofu.org/docs/intro/install/). I am using Fedora, so I installed OpenTofu with this command:
```bash
sudo dnf install opentofu
```
After that, you need to create a configuration file for OpenTofu. This file contains the configuration for the VM, including the image, shape, and networking settings. You can find an example configuration file in the [Oracle Provider documentation](https://registry.terraform.io/providers/oracle/oci/latest/docs). Creating every resource manually is a bit tedious, but you can use [module](https://github.com/oracle-terraform-modules/terraform-oci-compute-instance) created by Oracle to create the VM. Module is, lets say, a template for creating resources.

```hcl
module "instance" {
  source                      = "oracle-terraform-modules/compute-instance/oci"
  ad_number                   = 1
  instance_count              = 1
  compartment_ocid            = sensitive(local.secrets.oci_tenancy_ocid)
  instance_display_name       = local.oracle.vm.name
  source_ocid                 = local.oracle.vm.boot_img_ocid
  subnet_ocids                = [data.oci_core_subnet.hosting_subnet.id]
  public_ip                   = "NONE"
  ssh_public_keys             = fileexists("~/.ssh/id_rsa.pub") ? file("~/.ssh/id_rsa.pub") : file("~/.ssh/id_ed25519.pub")
  block_storage_sizes_in_gbs  = [local.oracle.vm.storage_size]
  boot_volume_size_in_gbs     = local.oracle.vm.storage_size
  shape                       = local.oracle.vm.shape
  instance_state              = "RUNNING"
  boot_volume_backup_policy   = "disabled"
  user_data = base64encode(templatefile("${path.module}/${local.oracle.vm.name}/cloud-init.yaml.tpl", {}))
}
```


This is part of the configuration file that I use to create the VM, let's go through it step by step:
 - `source`: Where to find the module. In this case, it is the Oracle module for creating a VM.
 - `ad_number`: This is the availability domain number, in this case it is 1. You can find the availability domain number in the Oracle Cloud console.
 - `compartment_id`: Compartment is a logical container for your resources. You can create compartments in the Oracle Cloud console and also find its ID there.
 - `instance_display_name`: VM name, no need to explain.
 - `source_ocid`: This is the ID of the image that you want to use for the VM. Let's say it is like an ISO image for the VM. You can find the image ID in the Oracle Cloud console.
 - `subnet_ocids`: Here you need to specify the subnet ID. Multiple subnets can be specified, but in my case I only have one subnet. You can find the subnet ID in the Oracle Cloud console.
 - `public_ip`: Here you can specify if you want to create a public IP address for the VM. I set it to `NONE`, because I will assign the public IP later.
 - `ssh_public_keys`: This is the public SSH key that you want to use to connect to the VM. Without this key you won't be able to connect to the VM.
 - `block_storage_size_in_gbs`: This is the size of the "disk" that you want to use for the VM. You can attach multiple disks to the VM.
 - `boot_volume_size_in_gbs`: If the previous option is the size of the "disk", this is the size of the "boot partition".
 - `shape`: Shape of the VM is specific name for  "this many cores and this much RAM". You can find the shape name in the Oracle Cloud console.
 - `instance_state`: Here you can specify if you want to create the VM in a stopped state or running state. I set it to `RUNNING`, because I want to use the VM right after it is created.
 - `boot_volume_backup_policy`: Here you can specify if you want to create a backup policy for the boot volume. I set it to `disabled`, because I don't need it.
 - `user_data`: This point will be explained later, but this is the script that will be executed when the VM is created. You can use it to install software, configure the VM, etc.

#### User data

The user data is a cloud-init file that is executed when the VM is created. In cloud-init file you can define if you want to update the system packages during the first boot, install additional packages, format, and mount additional disks, and create scripts that will be executed after the first boot. 

I used the cloud-init file to mount the additional block storage to my VM. This block storage is independent of the VM, so it will be preserved even if the VM is deleted. This allows me to replace the VM without losing the data. After that I install Docker and download `docker-compose` file from repository where I defined the services that I want to run on the VM.

Here comes handy the additional block storage. I used it to store the Docker volumes, so if I need to replace the VM, I can just attach the block storage to the new VM and all the data will be preserved. 

```yaml
fs_setup:
  - label: data_volume
    device: /dev/oracleoci/oraclevdz
    filesystem: ext4
    overwrite: false

mounts:
  - [ /dev/oracleoci/oraclevdz, /mnt/data, ext4, "defaults,noatime", "0", "2" ]
```

I used `fs_setup` option to format the block storage. It is important to set `overwrite: false` as this will prevent the block storage from being formatted again if it is already formatted. Then I mounted the block storage to `/mnt/data` directory. 

After that I created script file in `/usr/local/bin` directory. 

```yaml
write_files:
  - path: /usr/local/bin/init.sh
    permissions: '0755'
    content: |
      #!/bin/bash
      set -xe

      curl -s -X POST "https://api.telegram.org/bot${telegram_bot_token}/sendMessage" \
        -d "chat_id=${telegram_chat_id}&text=The init script has started on $HOSTNAME."

      START_TIME=$(date +%s)
        
      apt update -y && apt upgrade -y
```

I am using [Telegram Bot API](https://core.telegram.org/bots/api) to send messages from the script. This allows me to receive notifications about the status of the VM and any errors that occur during the creation process. After performing `apt update && apt upgrade`, I installed Docker and Docker Compose using commands from the [official Docker documentation](https://docs.docker.com/engine/install/ubuntu/).

Init script is executed by the `init.service` I created. This service ensures that the script is executed only once, when the VM is created, and not every time the VM is started.

```yaml
  - path: /etc/systemd/system/init.service
    permissions: '0644'
    content: |
      [Unit]
      Description=Init script
      After=network.target

      [Service]
      Type=oneshot
      ExecStart=/usr/local/bin/init.sh
      RemainAfterExit=true

      [Install]
      WantedBy=multi-user.target
```