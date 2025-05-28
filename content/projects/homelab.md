---
title: "Homelab"
date: 2025-05-25T21:19:00+02:00
draft: true
author: "MorganMLG"
tags:
  - Cloudflare
  - Codeberg
  - OpenTofu
  - Self-hosting
  - Networking
  - Proxmox
  - Docker
  - Home Assistant
  - Nextcloud
  - Plex
image: /images/projects/homelab/website.png
toc:
---
## 1. Homelab
Homelab is a personal project that I started back when I was still in high school. It has evolved over the years, and I have learned a lot about self-hosting, networking, and cloud computing. I use it to host various services, such as my personal website, a Nextcloud instance, and a Home Assistant instance. I also use it to experiment with new technologies and learn new skills. 

### 1.1. Why self-hosting?
I started self-hosting years ago with basic Samba shares on my old desktop. I just wanted to have place to store family photos and be able to access it from my laptop and my parents' computers. I hosted it on a Windows Server as I was not very familiar with Linux at the time. After a while, I added a Plex server to stream movies and TV shows. I then started to learn more about Linux and self-hosting, and I decided to switch to a Linux-based system. These first steps into self-hosting were a great success, and I realized that I could do much more with my home network, and be more independent from popular cloud services. Self-hosting opens up a world of possibilities, allowing me to run services that are tailored to my needs, also I don't want to rely on third-party services that can change their terms of service or shut down at any time. I want to have full control over my data and my services (of course as much as possible), and self-hosting allows me to do that.

### 1.2. Drawbacks of self-hosting
Self-hosting is not without its challenges. It requires some basic knowledge of networking, server administration, and security. It can also be time-consuming to set up and maintain, especially if over the time you gather a lot of services, and now not only you depends on it, but also your family and friends. You need to ensure that your services are secure and up-to-date, and you need to be able to troubleshoot any issues that arise. Sometimes you may feel that you are spending more time maintaining your services than actually using them, or working second full-time job. And I didn't even mention that you need to pay for the hardware, electricity, and internet connection. But I don't want to scare you away from self-hosting. The benefits often outweigh the drawbacks, and you can learn a lot in the process. Plus, another time when you will read about cloud-based services, that are shutting down, changing their terms of service, or restricting users access to content they have paid for, you will be happy that you have your own self-hosted services that you can rely on.

## 2. Hardware
Right now I have two locations where I host my services. The first one is at my home, where I spend most of my time, and the second one is at my parents' house. Also recently I have started to host some services on a VPS, including this website. I described it in details here: ["This website"](/projects/this_website/).

### 2.1. My home

| Hardware | Description |
| -------- | ----------- |
| Intel NUC Arena Canyon | This is my main server running Proxmox VE, which allows me to run multiple virtual machines and containers. |
| GMKtec G3 | This is my secondary server running Proxmox VE, which I use for hosting lighter workloads. |
| TP-Link TL-SG116E | This is my main network switch that connects most of my devices together. |
| TP-Link TL-SG108E | This is my secondary network switch that connects my TV and media devices. |
| Asus RT-AX58U | This is my main router that provides internet access to my home network. |
| Asus RT-AX53U | This is my secondary router that I use as AP in mesh mode. |
| CyberPower CP1500EPFCLCD | This is my UPS that protects my servers and network devices from power outages. |

This setup allows me to run multiple services and applications, while also providing redundancy and failover capabilities. I mainly use virtual machines with Docker installed on them to run most of my services. 