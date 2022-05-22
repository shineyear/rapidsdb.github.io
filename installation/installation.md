---
layout: default
title: Installation
nav_order: 3
has_children: true
---

# Installation

---

To install and use RapidsDB, hardware support is required. The following sections will list the RapidsDB configuration requirements as well as the recommended configurations for cluster deployment for your reference.

## Hardware Configuration

| Items            | Minimum Requirements                              | Recommended Requirements                          |
|:-----------------|:--------------------------------------------------|:--------------------------------------------------|
| CPU              | 2 x 2 Core                                        | 2 x 72 Core                                       |
| Memory           | 4GB                                               | 512GB                                             |
| Disk             | 100GB                                             | SSD 2TB                                           |
| Bandwidth        | 1,000MB                                           | 10GB/25GB                                         |
| Operating System | CentOS/RH 6.5 or above                            | CentOS/RH 7.6 or above                            |
| Machine          | Physical machine, virtual machine, Cloud platform | Physical machine, virtual machine, Cloud platform |

### CPU Configuration

A RapidsDB cluster consists of aggregator and leaf nodes. Each aggregator or leaf node is a process. Multiple aggregators and leaf nodes will consume a certain amount of CPU. As the CPU resource overhead can be very large when concurrent workloads are being processed, it is better to set up multi-core and multi-thread CPU configuration for a computing node when the hardware is configured.

### Memory Configuration

RapidsDB is a distributed in-memory database. The memory capacity directly determines the amount of data that can be loaded into it. Therefore, the memory configuration should be based on the actual data volume of a project. If massive data needs to be processed, it is recommended to load hot data into memory and store cold data on disk. As caching and temporary table storage will take up some memory space, it is better to reserve the space in advance before data is loaded.

### Disk Configuration

When a disk-based column store is utilized, the disk capacity needs to be considered as well as certain disk I/O requirements. The data compression technology can help save storage space and reduce I/O frequency to improve query performance. However, a regular data loading process requires disk I/O. As a result, a solid-state drive (SSD) is recommended. Furthermore, as the disk will also be used to store data backups, logs and other files, it is ideal to have a disk size which is at least twice as big as the data size.

### Bandwidth Configuration

As RapidsDB requires cluster deployment, communication between aggregator and leaf nodes is dependent on the bandwidth to ensure the data transmission efficiency. For handling massive data transactions and big clusters, an optical fiber network environment is recommended to guarantee the maximum performance of the database.

### Operating System Configuration

RapidsDB can be installed and deployed on many mainstream operating systems such as CentOS, Red Hat, etc. as well as a series of Chinese operating systems such as KylinOS, Hongqi Linux, Ningsi, etc.

## Cluster Configuration

### Cluster Size

A RapidsDB Cluster needs a master aggregator (master node, which only exists on a main server) to manage other sub-aggregators (slave node, each server can have multiple slave nodes). Each sub-aggregator on a server can have multiple leaf nodes.

The recommended number of aggregators depends on each specific uses case. For example, if a cluster is used for multiple types of workloads (for example, it is the backend of a web application as well as the object of an analytical query), it is recommended to have multiple aggregators or an aggregation pool for these independent workloads.

Besides workload distribution, the most important consideration should be network bandwidth. As a rule of thumb, a cluster with fifty or fewer nodes should have a leaf to aggregator node ratio of 5:1. A cluster with more than fifty nodes, the ratio can reach 10:1. Please note that you can add nodes to a cluster after the cluster is up and running in order to adjust the overall performance.

The appropriate ratio of aggregator and leaf nodes also depends on the type of operating workloads. Transactional workloads that run many small queries or queries involving only a single partition require more aggregator nodes because each query interacts with an aggregator and a leaf node, respectively. Analytical workloads, especially those involving distributed connections, require fewer aggregator nodes because almost all work is done on leaf nodes.

## Installation Details

Refer to [RapidsDB Installation and Management Guide](../downloads/RapidsDB_Installation_and_Management_Guide_Release_v4.3.3.pdf):

* Chapter 5 Installation Overview
* Chapter 6 Installing RapidsDB
