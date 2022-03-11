# 1 Hive介绍

是一种用类SQL语句来协助读写, 管理那些存储在分布式存储系统上的大数据集的数据仓库软件.

数据存储在HDFS上, Hive本身并不提供存储功能.

Hive将HDFS上的数据文件映射成数据库和一张张表, 而库和表的元数据信息存在关系型数据库上(MySQL)

能够存储很大的数据集, 并且对数据完整性, 格式要求不严格

因为Hive语句最终会生成MapReduce任务去计算, 所以不适用于实时计算的场景. 它适用于离线分析, 执行程序运行在yarn上.

# 2 Hive的优缺点

# 3 Hive与关系型数据库的区别

# 4 Hive体系结构

# 5 Hive查询过程分析

# 6 Hive的元数据分析

# 7 Hive的重要概念

## 	7.1 Hive的shell环境

# HiveQL

# Hive

# Hive

## 托管表和外部表

```shell
# 托管表
hive> load data inpath //user/temp/1.txt' into table managed_table;
# 则会将HDFS里该目录下的文件移动到该hive表对应的文件目录
hive> drop table managed_table;
# 则会将该文件彻底删除


# 创建外部表
hive> create external table external_table (str string)
hive> location '/user/temp/externla_table';	# 自动在HDFS创建该目录
hive> drop table external_table
#删表时只删除元数据，文件不会被删除
```

#### 7.6.2.1 分区

不同分区在HDFS里体现为不同目录, 分区列的名字就是文件夹名，文件数据里不含分区列

#### 7.6.2.2 桶 (bucket)

把表(或分区)组织成桶, 就是给它们加上了一列哈希值, 分为n个桶就对n取模, 提高查询效率

### 7.6.3 存储格式

**读频繁: 用列式存储**

**写频繁: 用行式存储**

