---
layout: default
title: Install RapidsDB with Docker Compose
parent: Installation
nav_order: 1
---

# How to Install RapidsDB

---

## Basic Information

Hardware

|:----------|:------------------------------------|
| Minimum   | 4 cpu + 32GB memory + 100GB storage |
| Recommend | 8 cpu + 128GB memory + 1TB storage  |

Operating System

|:------------------|
| CentOS 7.7 X86_64 |
| File system XFS   |

Please install docker + compose first

```shell
sudo yum update
sudo yum install docker docker-compose-plugin
```

## Installation

Upload rapidsdb package files to the server which u want to install

```shell
scp dws-docker-package-en [remote instance]
scp dvm-docker-package-stand [remove instance]
```

ssh login to remove instance to start rapidsdb

```shell
cd dvm-docker-package-stand
sudo docker compose up -d

cd dws-docker-package-en
sudo docker compose up -d
```










