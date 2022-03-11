# 常用命令

### 1 查看zk状态

```shell
cd /opt/cloudera/parcels/CDH/bin/
./zookeeper-server status
```

### 2 客户端

#### 连接zk

```shell
cd /opt/cloudera/parcels/CDH/bin/
./zookeeper-client
```

#### help 查看客户端帮助命令

#### ls 查看节点

#### get 获取节点数据和更新信息

#### stat

#### ls2 相当于ls加stat

#### create 创建节点

create [-s] [-e] path data acl

-s	创建带序列号的节点 sequence (创建的文件名带序列号)

-e	创建临时节点 ephemeral Owner=本次会话的ID	

用quit退出客户端后, 本文件自动删除	临时节点不能有自节点

path	指定znode路径 (必须是绝对路径)

data	znode携带的数据

acl	访问控制权限Access Control Lists

#### 修改节点

set path data [version]

#### delete

不能递归删除, 只能先删除子节点

#### rmr

可以递归删除

#### history 查询历史操作 && redo + 编号

#### connect 和 close

切换不同机器上的zookeeper	connect namenode-2:2182

### 3 nc 四字命令

auok	Are you OK?	imok	I am OK