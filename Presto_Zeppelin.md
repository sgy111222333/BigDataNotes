# Presto简介

# Presto部署

### 4.2 配置文件

coordinator和worker在同一节点：

```shell
# config.properties
coordinator=true
discovery-server.enabled=true
node-scheduler.include-coordinator=true

discovery.uri=http://namenode-1:8092
exchange.http-client.idle-timeout=150s
exchange.http-client.max-connections=400
exchange.http-client.request-timeout=150s
http-server.http.port=8092
node-manager.http-client.idle-timeout=150s
node-manager.http-client.request-timeout=150s
node-scheduler.max-pending-splits-per-node-per-stage=80
node-scheduler.max-splits-per-node=200
query.initial-hash-partitions=10
query.max-memory-per-node=128MB
query.max-total-memory-per-node=256MB
query.max-memory=1GB
task.concurrency=16
```



线上coordinator和worker要分开部署：

**coordinator:** node-scheduler.include-coordinator=false

**worker:** coordinator=false，删掉后两行

不同节点的node.properties不能一样

# zeppelin

