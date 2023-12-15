---
author: "Marcin M"
title: "How to Install Docker Desktop on Kali Linux"
date: 2023-11-06T18:32:45+01:00
description: "The steps below outline how to Docker Desktop on Kali Linux."
aliases: ["debian", "docker", "kalia"]
draft: true
categories: ["kali"]
tags: ["kali", "docker"]
ShowToc: true
TocOpen: false
searchHidden: false
editPost:
  URL: "https://github.com/Marcin13/posts/blob/master/Install-Docker-Desktop-on-Kali-Linux.md"
---

### Prerequisites

 Check the [requirements](https://docs.docker.com/desktop/install/linux-install/#system-requirements) for installing Docker Desktop before you can proceed.

#### 1. Update your system packages

```shell
sudo apt update
```

#### 2. Install required packages

```shell
sudo apt -y install apt-transport-https ca-certificates curl software-properties-common
```

#### 3. Download Docker GPG Key

```shell
curl -fsSL https://download.docker.com/linux/debian/gpg | \
sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/docker-archive-keyring.gpg
```

#### 4. Add Docker Repository

```shell
echo \
"deb [arch=amd64] https://download.docker.com/linux/debian bullseye stable" | \
 sudo tee /etc/apt/sources.list.d/docker.list
```

#### 5. Update your system packages again

```shell
sudo apt update
```

#### 6. Install Docker

```shell
sudo apt install docker-ce docker-ce-cli containerd.io uidmap
```

#### 7. Start Docker service

```shell
sudo systemctl start docker
```

#### 8. Add your user to the Docker group

```shell
sudo usermod -aG docker $USER
```

#### 9. Download Docker Desktop Package

>##### **Warning:** The latest known working version of Docker Desktop is reported to be **4.23.0**. However, using a version higher than 4.23.0 might cause issues, including potential damage to your Docker setup. I strongly recommend against installing a version beyond 4.23.0. If you choose to do so, be aware that it may adversely affect your Docker environment

```shell
wget https://desktop.docker.com/linux/main/amd64/docker-desktop-4.23.0-amd64.deb
```

#### 10. Install Docker Desktop Package

>##### **Warning:** Latest confirmed stable Docker Desktop version is **4.23.0**. Installing a version higher than 4.23.0 may cause issues and potentially damage your Docker setup. I strongly advise against installing versions beyond 4.23.0, as it can adversely affect your Docker environment

```shell
sudo apt install ./docker-desktop-4.23.0-amd64.deb
```

#### 11. Start Docker Desktop service (user level)

```shell
systemctl --user start docker-desktop
```

#### 12. Enable Docker Desktop service (user level)

```shell
systemctl --user enable docker-desktop
```

#### 13. Stop Docker Desktop service (user level)

```shell
systemctl --user stop docker-desktop
```

### Summary

These commands guide you through installing Docker on a Debian-based system. They cover updating packages, adding the Docker APT repository, installing Docker components, starting Docker services, and managing Docker Desktop at the user level. Ensure to check system requirements before proceeding.

![Screenshot.png](http://marcinmitruk.link/img/Install-Docker-Desktop-on-Kali-Linux/Screenshot1.png)
