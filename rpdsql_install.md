## How to install RPDSQL on a single node?

```shell
cd /opt/rdp
cp -fr /root/rdp/rpdsql-ops-2.0.5-dev-v.tar .
tar -xvf rpdsql-ops-2.0.5-dev-v.tar.gz
cd /rpdsql-ops

# install rpdsql agent
./rapids-manager agent-deploy -h 10.148.0.2 -t 22 -u root -s 123456 -p 9999

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
