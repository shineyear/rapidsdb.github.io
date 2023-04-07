---
layout: default
title: Install RapidsDB
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

Node info

| Node Name     | Public IP      | Private IP    | Node Role |
|:--------------|:---------------|:--------------|:----------|
| node1         | 18.144.169.18  | 172.31.4.129  | DQC       |
| node2         | 54.177.78.12   | 172.31.5.178  | DQE       |
| node3         | 54.193.208.14  | 172.31.12.199 | DQE       |

Port info

|:-----|
| 2888 |
| 3888 |
| 2181 |
| 4333 |
| 4334 |
| 22   |

## Installation

Setup file system

```shell
#mount your data volume to /opt or 
#if you using / as data storage then 

mkdir /opt
sudo chown centos:centos /opt
```

Disable selinux and upgrade system (repeat on each node)

```shell
ssh -vvi ~/.ssh/rapidsdb_ssh.pem centos@node_ip

sudo yum -y update

sudo vi /etc/sysconfig/selinux

#set selinux to disable
SELINUX=disabled

#need reboot
sudo reboot
```

Map each node (repeat on each node)

```shell
ssh -vvi ~/.ssh/rapidsdb_ssh.pem centos@node_ip

sudo vi /etc/hosts

#add at bottom
172.31.4.129  node1
172.31.5.178  node2
172.31.12.199 node3

sudo vi /etc/hostname

#replace the content to node name on each node
node name

sudo reboot
```

Setup keyless ssh login on each node

```shell
#run in each node
ssh-keygen -q -m PEM -t rsa -b 4096

cd ~/.ssh/

cat id_rsa.pub

#copy the cat result into authorized_keys on each node
vi authorized_keys

#verify on each node
ssh node1
ssh node2
ssh node3
```

Close huge page cache

```shell
sudo vi /etc/rc.d/rc.local

#add code
for f in /sys/kernel/mm/*transparent_hugepage/enabled; do
  if test -f $f; then echo never > $f; fi
done

for f in /sys/kernel/mm/*transparent_hugepage/defrag; do
  if test -f $f; then echo never > $f; fi
done

sudo chmod +x /etc/rc.d/rc.local

sudo reboot
```

Setup NTP

```shell
sudo yum -y install ntp net-tools

#must stop chronyd first , otherwise ntpd will not able to auto start
sudo systemctl disable chronyd.service

sudo systemctl start ntpd.service
sudo systemctl enable ntpd.service

ntpstat
```

Upload install package (repeat on each node)

```shell
scp -i ~/.ssh/rapidsdb_ssh.pem jdk-8u171-linux-x64.tar.gz centos@node_ip:/home/centos

scp -i ~/.ssh/rapidsdb_ssh.pem zookeeper-3.4.12.tar.gz centos@node_ip:/home/centos

scp -i ~/.ssh/rapidsdb_ssh.pem rdp-installer-R4.2.3.3-ge674a16.run centos@node_ip:/home/centos

scp -i ~/.ssh/rapidsdb_ssh.pem rapids.lic centos@node_ip:/home/centos
```

Install JDK (repeat on each node)

```shell
sudo mkdir -p /usr/local/java
cd /usr/local/java
sudo cp -fr /home/centos/jdk* .
sudo tar -vxzf jdk-8u171-linux-x64.tar.gz
sudo vi /etc/profile

#add
export JAVA_HOME=/usr/local/java/jdk1.8.0_171
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

cd /usr/local/java/jdk1.8.0_171
source /etc/profile
java -version

sudo yum install java
```

Kernel config

```shell
sudo vi /etc/sysctl.conf

#add
fs.file-max = 3145680
fs.epoll.max_user_instances = 8192
fs.inotify.max_user_watches = 16384
vm.max_map_count = 1048560

sudo sysctl -a
sudo vi /etc/security/limits.conf

#add
soft nproc 1045680
hard nproc 1045680
soft nofile 1045680
hard nofile 1045680

sudo vi /etc/security/limits.d/20-nproc.conf

#add
soft nproc 1045680
hard nproc 1045680
soft nofile 1045680
hard nofile 1045680
```

Install zookeeper (only on DQC node)

```shell
mkdir -p /opt/zookeeper/data
cd /opt/zookeeper/

cp -fr /home/centos/zookeeper-3.4.12.tar.gz .
tar -vxzf zookeeper-3.4.12.tar.gz

cd /opt/zookeeper/zookeeper-3.4.12/conf
cp zoo_sample.cfg zoo.cfg

vi zoo.cfg

#add
server.1=172.31.4.129:2888:3888

cd /opt/zookeeper/zookeeper-3.4.12/bin
./zkServer.sh start

#check log
cat zookeeper.out

sudo vi /usr/lib/systemd/system/zookeeper.service

[Unit]
Description=Zookeeper
After=syslog.target
 
[Service]
SyslogIdentifier=zookeeper
TimeoutStartSec=10min
Type=forking
ExecStart=/opt/zookeeper/zookeeper-3.4.12/bin/zkServer.sh start
ExecStop=/opt/zookeeper/zookeeper-3.4.12/bin/zkServer.sh stop
 
[Install]
WantedBy=multi-user.target
 
sudo systemctl enable zookeeper.service
```

Install RapidsDB cluster (repeat on each node)

```shell
mkdir -p /opt/rdp
cd /opt/rdp/

cp -fr /home/centos/rdp-installer-R4.2.3.3-ge674a16.run .
chmod 777 rdp-installer-R4.2.3.3-ge674a16.run

./rdp-installer-R4.2.3.3-ge674a16.run

cd /opt/rdp/current/cfg
vi zk.config.sample

#change to DQC node internal ip
nodeList = node_1_internal_ip:2181
```

cluster.config.sample:

```json
{
  "commonNodeConfig": {
    "enabled"               : true,
    "role"                  : "DQE",
    "clientPort"            : 4333,
    "clusterPort"           : 4334,
    "seEnabled"             : false,
    "seArgs"                : "",
    "sshUsername"           : "centos",
    "sshPathToIdentityFile" : "~/.ssh/id_rsa",
    "installationDir"       : "/opt/rdp",
    "workingDir"            : "/opt/rdp/current",
    "startupCommand"        : "./startDqx.sh",
    "shutdownCommand"       : "./stopDqx.sh"
  },

  "nodeConfig": [
    {
      "name"                : "node1",
      "role"                : "DQC",
      "hostname"            : "node_1_internal_ip"
    },
    {
      "name"                : "node2",
      "hostname"            : "node_2_internal_ip"
    },
    {
      "name"                : "node3",
      "hostname"            : "node_3_internal_ip"
    }
  ]
}
```

```shell
cd /opt/rdp
cp current/cfg/zk.config.sample zk.config
cp current/cfg/cluster.config.sample cluster.config

cp /home/centos/rapids.lic .
chmod +x rapids.lic
```

Start RapidsDB cluster (ssh into DQC node and run below)

```shell
cd /opt/rdp/current
./bootstrapper.sh -a install -i ../rdp-installer-R4.2.3.3-ge674a16.run -l ../rapids.lic

./bootstrapper.sh -a start
```
