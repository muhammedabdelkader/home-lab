# 🧪 My Home Lab Setup

Welcome to my self-hosted lab — a playground for learning, automation, media streaming, and security testing. This setup runs on **Raspberry Pi**, **Proxmox**, and external storage, tied together with **Docker**, **Portainer**, and **Cloudflare Tunnels**.

- 🔐 Authenticated access via GitHub OAuth + NGINX  
- 🌩️ Reverse proxy + automatic HTTPS  
- 📡 Monitored by alerting stack with notifications  
- 🎥 Streaming, 📚 reading, 📁 syncing — all automated  
- 🧠 Integrated with a private dashboard + VS Code in browser  

---

## 🧱 Infrastructure Stack

Core services that make everything else possible.

### 🔐 NGINX Proxy Manager
```yaml
image: jc21/nginx-proxy-manager:latest
ports:
  - '80:80'
  - '81:81'
  - '443:443'
```
### ☁️ Nextcloud
```yaml
image: lscr.io/linuxserver/nextcloud:latest
volumes:
  - /mnt/.docker/nginx/nextcloud/appdata:/config
  - /mnt/.docker/nginx/nextcloud/data:/data
```
### 🏡 Home Assistant
```yaml
image: lscr.io/linuxserver/homeassistant:latest
volumes:
  - /mnt/.docker/nginx/hass/config:/config
```
### 🛡️ OAuth2 Proxy (GitHub)
```yaml
 
image: quay.io/oauth2-proxy/oauth2-proxy:v7.6.0
ports:
  - "4180:4180"
# Note: Secrets have been redacted
```
### 🧑‍💻 Code Server
```yaml
image: lscr.io/linuxserver/code-server:latest
volumes:
  - /mnt/.docker/code-server/config:/config
```
### 📋 Lab Dash
```yaml
image: ghcr.io/anthonygress/lab-dash:latest
volumes:
  - /var/run/docker.sock:/var/run/docker.sock
```
### 📄 Stirling PDF
```yaml
image: frooodle/s-pdf:latest
volumes:
  - /mnt/.docker/pdf/stirling-data:/usr/share/tessdata
```
### 🧠 WIP (Personal Tracker)
```yaml
image: eigenfocus/eigenfocus:1.2.0-free
```
## 🎞️ Media Stack
Automated content pipeline — books, movies, and torrents.

### 🌊 qBittorrent
```yaml
image: lscr.io/linuxserver/qbittorrent:latest
volumes:
  - /mnt/nfs-media/downloads:/downloads
```
### 📚 Readarr + Calibre Web
```yaml
image: lscr.io/linuxserver/readarr:develop
volumes:
  - /mnt/nfs-media/books:/books
  - /mnt/nfs-media/downloads:/downloads
image: linuxserver/calibre-web
volumes:
  - /mnt/nfs-media/books:/books
```
### 🎬 Radarr + Jellyfin
```yaml
image: lscr.io/linuxserver/radarr:latest
volumes:
  - /mnt/nfs-media/movies:/movies
  - /mnt/nfs-media/downloads:/downloads
image: jellyfin/jellyfin:latest
volumes:
  - /mnt/nfs-media/movies:/media/movies
```
### 🗣️ Bazarr (Subtitles)
```yaml
image: lscr.io/linuxserver/bazarr:latest
volumes:
  - /mnt/nfs-media/movies:/movies
  - /mnt/nfs-media/tv:/tv
```
### 🔍 Jackett
```yaml
image: lscr.io/linuxserver/jackett:latest
volumes:
  - /mnt/nfs-media/jackett/config:/config
  - /mnt/nfs-media/downloads:/downloads
```
## 📈 Monitoring Stack
Know when anything goes down. Instantly.

### 📳 Gotify + iGotify
```yaml
image: gotify/server
volumes:
  - /mnt/.docker/gotify-data:/app/data
```yaml
image: ghcr.io/androidseb25/igotify-notification-assist:latest
# Note: Secrets have been redacted
```
### 📟 Uptime Kuma
```yaml
image: louislam/uptime-kuma:latest
volumes:
  - /mnt/.docker/uptime-kuma:/app/data
```
### 🚨 Alertmanager
```yaml
image: prom/alertmanager
volumes:
  - /mnt/.docker/alertmanager:/etc/alertmanager
```
### 🧩 Networking
All services share the same secure network:
```yaml
networks:
  shared-net:
    external: true
```

## 🔐 Secrets & Security
GitHub OAuth secrets and notification tokens have been redacted

Services like OAuth2 Proxy, Gotify, and iGotify require proper environment setup for secure production use

## 📁 Create Required Docker Volume Directories
Before running your Docker Compose stacks, make sure all the necessary host directories exist.
Use this script to automate creating them.

## 🛠️ Step-by-Step Setup
Save the script below as create-docker-volumes.sh

Make it executable

```bash
chmod +x create-docker-volumes.sh
```
Run the script

```bash 
./create-docker-volumes.sh
```
### 📜 create-docker-volumes.sh
```bash

#!/bin/bash
set -e
# Create all necessary volume directories for the home lab
mkdir -p "/mnt/.docker/alertmanager"
mkdir -p "/mnt/.docker/bazarr/config"
mkdir -p "/mnt/.docker/calibre-web/config"
mkdir -p "/mnt/.docker/code-server/config"
mkdir -p "/mnt/.docker/eigenfocus/app-data"
mkdir -p "/mnt/.docker/gotify-data"
mkdir -p "/mnt/.docker/gotify-data/api-data"
mkdir -p "/mnt/.docker/homepage/config"
mkdir -p "/mnt/.docker/jellyfin/config"
mkdir -p "/mnt/.docker/lab-dash/config"
mkdir -p "/mnt/.docker/lab-dash/uploads"
mkdir -p "/mnt/.docker/nginx/data"
mkdir -p "/mnt/.docker/nginx/letsencrypt"
mkdir -p "/mnt/.docker/nginx/hass/config"
mkdir -p "/mnt/.docker/nginx/nextcloud/appdata"
mkdir -p "/mnt/.docker/nginx/nextcloud/data"
mkdir -p "/mnt/.docker/nginx-stack"
mkdir -p "/mnt/.docker/pdf/configs"
mkdir -p "/mnt/.docker/pdf/stirling-data"
mkdir -p "/mnt/.docker/qbittorrent/config"
mkdir -p "/mnt/.docker/radarr/config"
mkdir -p "/mnt/.docker/readarr/config"
mkdir -p "/mnt/.docker/uptime-kuma"
mkdir -p "/mnt/nfs-media/books"
mkdir -p "/mnt/nfs-media/downloads"
mkdir -p "/mnt/nfs-media/jackett/config"
mkdir -p "/mnt/nfs-media/movies"
mkdir -p "/mnt/nfs-media/tv"
```
## ✅ What This Script Does
### Creates all Docker volume paths required by:

* 🧱 Infrastructure stack

* 🎞️ Media stack

*  📈 Monitoring stack

* Avoids permission issues at runtime

* Keeps host filesystem organized under /mnt/.docker/ and /mnt/nfs-media/

> You can modify the paths if your setup uses different mount points or external drives.

## 🚀 How to Use This Repository
This repository contains all the stack definitions and tools for spinning up your self-hosted home lab.

### 📦 Folder Structure
```text
.
├── README.md                        # 📘 You’re reading it!
├── create-docker-volumes.sh         # 📁 Directory setup script
├── infrastructure-stack/            # 🧱 Core infrastructure services
│   └── Dockerfile
├── media-stack/                     # 🎞️ Media automation and streaming
│   └── Dockerfile
├── monitoring-stack/                # 📈 Notifications and uptime tracking
│   └── Dockerfile
```
### 🔧 Prerequisites
    ✅ Docker + Docker Compose installed

    ✅ /mnt/.docker/ and /mnt/nfs-media/ available on host

    ✅ Optional: NFS share or external storage mounted

    ✅ (Recommended) Reverse proxy and DNS management via NGINX Proxy Manager + Cloudflare

## 🧰 Setup Instructions
### Clone the repository

```bash 
git clone https://github.com/your-username/home-lab.git
cd home-lab
```
### Create required directories

```bash
chmod +x create-docker-volumes.sh
./create-docker-volumes.sh
```
### Configure .env files (if needed)
    Add secrets like GitHub OAuth or Gotify tokens in a secure .env file or use secrets manager.

### Deploy stacks
You can use docker compose or Portainer for each stack:

```bash
cd infrastructure-stack
docker compose up -d

cd ../media-stack
docker compose up -d

cd ../monitoring-stack
docker compose up -d
```
## Access services
| Stack         | Service              | Access URL Example              |
|---------------|----------------------|---------------------------------|
| Infrastructure | NGINX Proxy Manager  | https://proxy.yourdomain.com    |
| Media         | Jellyfin             | https://media.yourdomain.com    |
| Monitoring    | Uptime Kuma          | https://status.yourdomain.com   |


## ✨ Final Words
This home lab project is not only a passion but a learning platform. It helps me:

* Practice infrastructure automation

* Self-host powerful alternatives to SaaS tools

* Monitor uptime and notifications

* Stay sharp for bug bounty and security testing

## 📬 Support & Contributions
Have suggestions, issues, or ideas?
Feel free to open an issue or submit a pull request!

 > # Happy homelabbing! 🧠🧪🚀