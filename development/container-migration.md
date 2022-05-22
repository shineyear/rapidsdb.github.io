---
layout: default
title: RapidsDB Container Migration
parent: Development
nav_order: 9
---

# RapidsDB Container Migration

---

```shell
docker ps

docker images

docker commit --author shineyear --message "create the rapids db migration image" dqc1 rapidsdb:1

docker save -o rapidsdb_dqc1 rapidsdb:1

tar -czvf rapids_dqc1.tar.gz rapidsdb_dqc1

ls -alh

-rw-r--r--   1 root root 1.4G Nov 21 11:53 rapids_dqc1.tar.gz

scp root@ip:/root/rapids_dqc1.tar.gz .

tar -vxzf rapids_dqc1.tar.gz

docker load -i rapidsdb_dqc1

docker images

docker network ls

docker run -d --restart always --net rapids_net --ip 193.168.1.1 -p 12181:2181 -p 11122:22 -p 14333:4333 -p 4334:4334 -p 19999:9999 -p 13306:3306 -p 13307:3307 -p 13308:3308 -p 13309:3309 --name dqc1 rapidsdb:1
```
