---
layout: default
title: Install RPDSQL
parent: Installation
nav_order: 2
---

# How to Install RPDSQL

---
download ur rpdsql-ops-4.3.7-dev-v.tar to /root or upload from ur local computer to /root then

```shell
cd /opt/rdp
cp -fr /root/rpdsql-ops-4.3.7-dev-v.tar .
tar -vxf rpdsql-ops-4.3.7-dev-v.tar
cd rpdsql-ops-4.3.7-dev-v

# install rpdsql agent
# rpdsql install currently only support username/password login
# if you want to install multi node , please first install locally
./rapids-manager agent-deploy -h your_internal_local_ip -t 22 -u root -s 123456 -p 9999

# list all agent to get the agent_id like: A97d023a23a4a4089b319d1723629387e
./rapids-manager agent-list

# input "agent_id", input role "MASTER", input port "3306"
./rapids-manager rpdsql-install

# input "agent_id", input role "AGGREGATOR", input port "3307"
./rapids-manager rpdsql-install

# input "agent_id", input role "LEAF", input port "3308"
./rapids-manager rpdsql-install

# input "agent_id", input role "LEAF", input port "3309"
./rapids-manager rpdsql-install

# list all rpdsql process
./rapids-manager rpdsql-list

# init client connections
./rapids-manager rpdsql-init-client

# login to rpdsql
./rapids -P3306
```
