# 简介

​		Airflow是一个可编程，调度和监控工作流的平台，基于有向无环图（DAG），Airflow可以定义一组有依赖的任务，按照**依赖**依次执行。Airflow提供了丰富的**命令行工具**用于系统管控，而其**Web管理界面**同样可以方便的管控调度任务，并且对任务运行状态进行实时监控，方便了系统的运维和管理。

**类似软件：Oozie	Azkaban**



### 特点

- 灵活, 基于代码级的定义
- 可扩展性强
- 工作流定义清晰简洁
- 伸缩性好

# 部署

### **杀掉所有相关进程:**

`ps -ef |grep airflow|grep -v grep |awk '{print $2}' | xargs kill -9`

# 基本组件



# 创建工作流

