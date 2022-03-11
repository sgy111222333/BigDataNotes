# 1 ansible部署

### 简介

ansible是一个轻量级的运维自动化工具，基于Python研发，实现了批量系统配置、批量程序部署、批量运行命令等功能。在管理主机上部署ansible，它使用SSH连接到被管理主机并运行配置好的任内，被管理主机不需要装任何软件，只要有SSH就行。

### 部署

免密：

管理者的公钥，追加到被管理者的authorized_keys里

# 2 ansible使用

### 1.ping模块



### 2.file模块



### 3.copy模块

```shell
ansible tgroup -m copy -a "src=/home/zzx dest=/home/sgy"
```

### 4.cron模块



### 5.yum模块



### 6.group模块

创建、删除用户组



### 7.user模块



### 8.unarchive模块



### 9.script模块

在被管理主机上执行在管理主机上的脚本

### 10.shell模块

执行在被管理主机上的脚本



### 11.command模块

直接执行命令

# 3 ansible playbook

![image-20220308111119550](C:\Users\sgy11\AppData\Roaming\Typora\typora-user-images\image-20220308111119550.png)





