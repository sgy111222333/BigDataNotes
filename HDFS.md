# 1 介绍

- 分布式文件系统 (distributed filesystem) :

- HDFS

一个文件在集群里默认存三份 ( 挑三台机器每台存一份, 自动同步 ) 多人访问时, 访问不同机器的同一个文件

若文件大于剩余空间, 把文件拆分成块 (block) 默认一块128MB, 其实是把每一块备份三次

# 2 设计原则

### 目标: 

1 存储非常大的文件	(分块机制)

2 采用流式的数据访问方式	流处理<==>批处理	(流处理: 迅雷边看边下	批处理: 全下完再看)

3 运行在商业硬件上

### 不适合: 

1 低延时的数据访问 (毫秒级)	（低延时用Hbase）

2 大量小文件 文件元数据也占用空间, 如 目录结构,文件block 存在NameNode的内存中

​	NameNode内存决定了能存文件个数的上限	每个文件/目录/文件块都会产生一条元数据, 一条元数据大约150B, 100万个块的元数据大约300M, 十亿条就是300G内存, 一般服务器不够.

(文件有一条元数据, 文件产生的块又有一条)

3 多方读写, 任意文件修改

HDFS采用追加 (append-only) 方式写入数据, 不支持任意修改, 不支持同时写入

# 3 核心概念

## 1  数据块 Block

默认一块是128M, 比Block小的文件, 不会占满一个块

### HDFS的Block这么大的原因:

最小化寻址开销

假设要用10ms定位到Block, 磁盘传输速度为100MB/s. 要使寻址时间占传输时间的1%, 则一块的大小应该设为100MB左右, ( 寻址10ms, 传输1000ms ).

### Block抽象的好处:

1. 单个文件大小可以大于单机磁盘的容量, 构成文件的Block块可以分布在整个集群上.
2. Block作为**容错和高可用机制中的副本单元**, 即以Block为单位进行复制.

## 2 NameNode 与 DataNode

### NameNode (管理节点)	

构建命名空间, **管理元数据** 等

存放文件系统树及所有文件 目录的元数据

**元数据持久化**: 

镜像文件	fsimage_xxxxxxx	HDFS文件系统元数据的一个永久性检查点, 后面的数字表示本文件最后一个事务

编辑日志	edits_xxxxxx			存放HDFS的所有**更新操作**

> 日志存操作, 镜像存操作的结果

**持久化数据里不包括Block存放在那台机器上, Block位置是存在DataNode上**	

**NameNode重启后, DataNode主动向NameNode报告 Block分布信息**

![image-20220212113146203](C:\Users\sgy11\AppData\Roaming\Typora\typora-user-images\image-20220212113146203.png)

### DataNode (工作节点)	

负责实际存储数据, 负责读写工作



# 4 操作

**HDFS 只有绝对路径, 没有相对路径, 也没有 cd 命令**	命令选项不支持连写	如: `ls -lrth` 会报错

```shell
HADOOP_USER_NAME=hdfs hdfs dfs -chmod -R 777 /user
hdfs dfs -mkdir -p /user/test/t1/t2
```

**从Linux传文件到HDFS: 	copyFromLocal	或	put**

```shell
hdfs dfs -copyFromLocal ./*.out /user/test
hdfs dfs -put ./sort_test.txt /user/test
```

**从HDFS传文件到Linux: 	copyToLocal	或	get**	

```shell
hdfs dfs -copyToLocal /user/test/1.txt /home/sgy/
hdfs dfs -get /user/test/1.txt /home
```

**删除**

```shell
hdfs dfs -rm -skipTrash /user/test/1.txt	# skipTrash 跳过回收站（默认在回收站保存3天）
```

**setrep	设置副本数, 只对本文件生效**	**在CM里设置复制因子, 对所有后加入的文件都生效**

```shell
hdfs dfs -setrep -w 2 /user/test/test_supervisor.out
```

**stat	返回指定路径的统计信息**

# 5 读写流程

![image-20220214191413924](C:\Users\sgy11\AppData\Roaming\Typora\typora-user-images\image-20220214191413924.png)





# 6 相关运维工具

### dfsadmin

```shell
HADOOP_USER_NAME=hdfs hdfs dfsadmin -report
```

回显的内容相当于查看 http://namenode-1:50070/	(本网页与CM无关, 是Hadoop自带的)

### fsck

检查HDFS中文件的健康状况

```shell
HADOOP_USER_NAME=hdfs hdfs fsck /		# 查找缺失的块或者副本过多的块
HADOOP_USER_NAME=hdfs hdfs fsck -list-corruptfileblocks /		# 查找坏块
hdfs fsck /user/test/test_supervisor.out -files -blocks -racks	# 查看文件对应的块

```

jvm 周志明 

# 7 企业级配置