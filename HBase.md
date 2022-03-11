# 一. HBase概述

分布式数据库系统

列式存储

![image-20220302195142937](C:\Users\sgy11\AppData\Roaming\Typora\typora-user-images\image-20220302195142937.png)

# 二. HBase基本概念

### 列族

一个表有多个列族，每个列族可以有任意个列

# 三. HBase体系架构

# 四. HBase物理存储结构

1. Table中所有行都按照rowkey的字典序排序
2.  Table在行的方向上分割为多个region
3. region是按大小分割的，每一个表刚开始只有一个region
4. HRegion是Hbase中分布式存储和负载均衡的最小单元
5. HEegion虽然是**分布式存储**的最小单元，但不是**存储**的最小单元。事实上HRegion由一个或多个Store组成，每个store保存一个columns family。每个Store又由一个memStore和0至多个StoreFile组成，StoreFile就是存在HDFS上的具体文件



## HLog

先写HLog再写MenStore，持久化到StoreFile后删除HLog

如果RegionServer挂了，



# 五. HBase的Web UI

# 六. HBase读写流程

# 七. HBase的RowKey设计

## 设计原则

散列不开————热点问题

# 八. HBase的shell操作

# 九. HBase表和Hive表交互



Hive创建外部表，关联HBase的文件就行



```shell


```









