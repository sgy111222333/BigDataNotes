# 1 yarn的介绍

​		Hadoop集群的资源调度系统. Hadoop 1.x的资源由MapReduce负责, 2.x引入了yarn.

​		很少直接用代码调用 yarn 的 API, 而是通过 MapReduce, Spark, Tze 等分布式计算框架 间接使用 yarn.

# 2 yarn的优势

| 优点             | 描述                                                 |
| ---------------- | ---------------------------------------------------- |
| 支持多种计算框架 | 只要实现了yarn接口的计算框架都可以运行在yarn上       |
| 资源利用率高     | 多种计算框架可以共用一套集群资源, 提高资源利用率     |
| 运维成本低       | 避免一个框架一个集群的模式, yarn降低了集群的运维成本 |
| 数据可共享       | 多种框架共享数据和硬件资源, 减少数据移动带来的成本   |

# 3 yarn的系统架构

### ResourceManager

RM负责处理客户端请求, 对NM上的资源进行统一管理和调度. 

RM给NM上的ApplicationMaster分配Container并监控其运行.

RM由调度器Scheduler和ApplicationManager组成

### NodeManager

每个节点都有NM, 它会定时向RM汇报本节点上的资源使用情况和各个Container的运行状态

NM接收并处理来自ApplicationMaster的Container启动/终止等请求

### ApplicationMaster

用户提交的每个应用程序都有一个AM, 负责监控应用, 向RM协调资源, 与NM协同完成任务的执行和监控

### Container

封装了多种资源, 当ApplicationMaster向Rm申请资源时, RM为ApplicationMaster返回用Container封装的资源

**(ApplicationsManager就一个, ApplicationMaster有多个)**

<img src="C:\Users\sgy11\AppData\Roaming\Typora\typora-user-images\image-20220309145124427.png" alt="image-20220309145124427" style="zoom: 72%;" />

# 4 yarn提交任务过程分析

1. 客户端向RM提交计算任务
2. 启动第一个Container，在里面启动AppMaster
3. 向ApplicationsManager注册AppMaster
4. 申请计算资源
5. 以Contaioner形式回复资源列表

# 5 yarn的HA

# 6 yarn的调度器

# 7 yarn的企业级配置

# 8 yarn的命令

