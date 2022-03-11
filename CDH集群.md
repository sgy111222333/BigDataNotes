#                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             一. Hadoop简介

狭义: Apache Hadoop

广义: Hadoop 生态系统

优势: 高可靠, 高扩展, 高容错, 高效

Hadoop2.x 组成: MapReduce (计算)	Yarn (资源调度)	HDFS (数据存储)	Common (辅助工具)

### 发行版本

Apache Hadoop	完全开源免费, 社区活跃, 文档齐全, 版本混乱

CHD: Cloudera's Distribution including Apache Hadoop	免费, 版本管理清晰, 使用 Cloudera Manager 进行安装 部署 配置; 方便 效率高	高级功能要花钱

HDP: Hortonworks Data Platform 完全开源免费; 使用Ambari安装部署配置; 方便高效	相对不稳定

镜像网站:https://ro-bucharest-repo.bigstepcloud.com/cloudera-repos/

# 二. 搭建

## 2.1 主机入集群操作

### 1 修改hostname和hosts

三台的名字分别是	namenode-1	namenode-2	storage-1

```shell
hostname namenode-1
vi /etc/hostname	#清空，改为namenode-1
#然后重连ssh

vim /etc/hosts
#添加
192.168.10.136 namenode-1
192.168.10.138 namenode-2
192.168.10.139 storage-1
```

### 2 关闭防火墙

```shell
systemctl stop firewalld
systemctl status firewalld		#检查是否关闭
systemctl disable firewalld 	#关闭开机自启动
systemctl is-enabled firewalld	#查看是否开机自启动
```

### 3 关闭selinux

```shell
getenforce
setenforce 0	#关闭
vi /etc/selinux/config
#把enforcing改成disabled
```

### 4 关闭NetworkManager

```shell
systemctl stop NetworkManager
systemctl disable NetworkManager
```

### 5 配置yum源

```shell

```

### 6 配置ntp

```shell
#备份/etc/ntp.conf 并修改	(主从节点的配置文件不一样)

#重启服务, 开机自启动

ntpdate 主节点ip
ntpstat
ntpq -p	#时钟同步连接状态
```

### 7 配置免密登录

### 8 配置ulimit

```shell
ulimit -n 1024000	#可打开的最大文件数
ulimit -u 1024000	#当前用户可启用的最大进程数
vi /etc/security/limits.conf
#追加
* - nofile 1024000
* - nproc 1024000
```

### 9 配置vm (关闭虚拟内存机制)

```shell
vi /etc/sysctl.cong
#追加
vm.swappiness=0
vm.overcommit_memory=1

sysctl -p	#使配置文件生效
```

### 10 配置fastab, 关闭swap

```shell
# 新来一块磁盘,把配置属性写到哪个配置文件?	fs==>file system  存磁盘和目录的映射关系
vi /etc/fstab
# 注释掉有swap那行
swapoff -a
free -h	#swap那行应该是0
```

### 11 配置defrag, 关闭透明大页

```shell
# 内存碎片整理, 影响性能
cat /sys/kernel/mm/transparent_hugepage/enabled
cat /sys/kernel/mm/transparent_hugepage/defrag
echo never > /sys/kernel/mm/transparent_hugepage/enabled
echo never > /sys/kernel/mm/transparent_hugepage/defrag
# 开机自启动
/etc/rc.d/rc.local #添加上两句
chmod +x /etc/rc.d/rc.local
```

### 12 磁盘格式化及挂载 ----线上多磁盘环境才需要, 暂时不用

```shell
# 1.查看有几个盘
fdisk -l	
# 2.格式化:
mkfs.ext4 /dev/sdb
mkfs.ext4 /dev/sdc
# 3.创建数据目录
mkdir -p /usr/data/nn/1
mkdir -p /usr/data/nn/2
# 4.挂载磁盘到目录(前面是磁盘名 后面是目录)
mount /dev/sdb /usr/data/nn/1
mount /dev/sdc /usr/data/nn/2
df -h	#查看详情Mounted on
# 5.查看UUID(通用唯一识别码)和磁盘格式,如ext4
blkid
# 6.在/etc/fstab中添加:
UUID=xxx /usr/data/nn/1 ext4 acl,usr_xatrr,nodiratime 1 2
# (xxx就是第五步的UUID)
# /etc/fstab里有 根目录,/boot目录 的UUID 新挂载的磁盘必须写到这里面!
```

### 13 安装 配置jdk

```shell
# 不用先清空已有jdk?
cd /usr/
mkdir java
#将jdk-8u162-linux-x64.tar.gz解压到/usr/java
tar -zxvf jdk-8u261-linux-x64.tar.gz -C /usr/java
# 配置jdk环境变量 在/etc/profile里添加
JAVA_HOME=/usr/java/jdk1.8.0_162
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
PATH=$JAVA_HOME/bin:$PATH
export JAVA_HOME CLASSPATH PATH

source /etc/profile
java -version
```

### 14 安装 配置 启动 cm-server

```shell
#每个节点都要装
yum install httpd -y #先安装http
#namenode-1全装，其他只装daemons和agent
rpm -ivh cloudera-manager-daemons-5.16.1-1.cm5161.p0.1.el7.x86_64.rpm	#daemons用来监控另外三个，有拉起功能，类似supervisor
rpm -ivh cloudera-manager-server-5.16.1-1.cm5161.p0.1.el7.x86_64.rpm	#server是web界面
rpm -ivh cloudera-manager-server-db-2-5.16.1-1.cm5161.p0.1.el7.x86_64.rpm	#基于postgresql
rpm -ivh cloudera-manager-agent-5.16.1-1.cm5161.p0.1.el7.x86_64.rpm 	#采集服务器运行状态

# 其中安装server-db时提示没有postgresql-server
# error: Failed dependencies:
#	  postgresql-server >= 8.4 is needed by cloudera-manager-server-db-2-5.16.1-1.cm5161.p0.1.el7.x86_64
# 安装postgresql三个文件
rpm -ivh postgresql84-libs-8.4.21-1PGDG.rhel6.x86_64.rpm
rpm -ivh postgresql84-8.4.21-1PGDG.rhel6.x86_64.rpm
rpm -ivh postgresql84-server-8.4.21-1PGDG.rhel6.x86_64.rpm

# 安装agent时缺10个依赖
#	bind-utils is needed by cloudera-manager-agent-5.16.1-1.cm5161.p0.1.el7.x86_64
#	psmisc is needed by cloudera-manager-agent-5.16.1-1.cm5161.p0.1.el7.x86_64
#	cyrus-sasl-plain is needed by cloudera-manager-agent-5.16.1-1.cm5161.p0.1.el7.x86_64
#	cyrus-sasl-gssapi is needed by cloudera-manager-agent-5.16.1-1.cm5161.p0.1.el7.x86_64
#	portmap is needed by cloudera-manager-agent-5.16.1-1.cm5161.p0.1.el7.x86_64
#	/lib/lsb/init-functions is needed by cloudera-manager-agent-5.16.1-1.cm5161.p0.1.el7.x86_64
#	mod_ssl is needed by cloudera-manager-agent-5.16.1-1.cm5161.p0.1.el7.x86_64
#	openssl-devel is needed by cloudera-manager-agent-5.16.1-1.cm5161.p0.1.el7.x86_64
#	python-psycopg2 is needed by cloudera-manager-agent-5.16.1-1.cm5161.p0.1.el7.x86_64
#	MySQL-python is needed by cloudera-manager-agent-5.16.1-1.cm5161.p0.1.el7.x86_64
yum install -y bind-utils psmisc cyrus-sasl-plain cyrus-sasl-gssapi portmap lsb mod_ssl openssl-devel python-psycopg2 MySQL-python 
```



### 15 安装 配置 启动 cm-agent

```shell
vim /etc/cloudera-scm-agent/config.ini	#serverhost改成namenode-1 三台都是
```



## 2.2 CM server 和 agent服务

```shell
# namenode-1
service cloudera-scm-server-db start
service cloudera-scm-server start
service cloudera-scm-agent start	#（其它两个只启agent）
#(daemons是自己运行的)

#查看server监听的端口
ps -ef | grep /var/log/cloudera-scm-server	#得到server的pid
netstat -nap | grep pid						# 7180
```

## 2.3 CM 界面

```shell
#在namenode-1
cd /opt/cloudera
# parcel-repo 是仓库，里面放.parcel .sha manifest.json文件
CDH-5.7.1-1.cdh5.7.1.p0.11-el7.parcel	#里面是所有hadoop大数据服务的程序 .sha存hash值用于校验
manifest.json	#货单文件，描述了CDH里具体的服务信息
# 制作仓库
# 把CDH.parcel CDH.sha manifest.json 拷贝到/opt/cloudera/parcel-repo，
chown cloudera-scm:cloudera-scm (三个文件)
```

### 在CM网页

集群安装->更多->远程Parcel存储库URL全删掉

重开一个CM网页->点主页右上角礼包图标->点右上角的配置->更新频率改为1分钟

# 三. CM管理

高可用: high availability	一主一备

### hdfs

高可用选namenode-2

### yarn

高可用选namenode-2

# 四. 升级CM和CDH服务的jdk

为了不影响其他非CDH服务，可以**在不修改jdk环境变量的情况下**，

**只升级CM和CDH服务的jdk，超级实用！**

```shell
#————————————————————更新namenode-1中CM server的jdk——————————————————————————————
# jdk还是解压到/usr/java/
ps -ef|grep /var/log/cloudera-scm-server#后面是进程的执行命令，查看这里jdk路径
# 修改cloudera-scm-server配置文件，添加jkd路径
vim /etc/default/cloudera-scm-server
#Java Options。
export JAVA_HOME=/usr/java/jdk1.8.0_261
# 重启cloudera-scm-server服务
service cloudera-scm-server restart
# 再看进程，后面命令的jdk就变了
ps -ef|grep /var/log/cloudera-scm-server
#————————————————————更新CDH中hdfs、zookeeper等服务的jdk————————————————————
在CM网页->主机->所有主机->配置->高级->java主目录
在CM主页g，不能一次性全重起！
```

agent是python进程，不需要jdk

# 包的存放位置

### node1

jdk:	/home/cm_parcels

cm:	/home/cm_parcels/cm-rpm/cloudera-manager

### node2

jdk和cm都在	/home/cm_agent_install





# 五. parcel/csd 二次开发

可以使用CM管理非Cloudera提供的第三方服务

https://github.com/cloudera/cm_ext/wiki

pom.xml里的test改成自己名字

shell里减号的作用: 指定默认值

assemble.sh

```shell
pushd .	#把当前目录放存到堆栈, 放在开头
popd	#返回刚才存的目录, 放在结尾			vim反撤销:ctrl+r
```



# 六. parcel/csd 讲解

## Parcel

修改test-parcel里pom.xml	test换为sgy

修改/home/test-parcel/src/main/resources/assemble.sh	test换为sgy	hash=自研

修改/home/test-parcel/src/main/resources/meta/四个文件里的test全换为sgy

修改/home/test-parcel/package里的包名,使其与assemble.sh里sgy_download_name="sgy-1.0.0.tar.gz"对应

修改/home/test-parcel/src/main/resources/meta里test_env.sh文件名,使其于与/home/test-parcel/src/main/resources/meta/parcel.json里"defines": "sgy_env.sh"对应

共修改了两个文件名, 还有package里可以解压后再重命名

```shell
cd /home/test-parcel/
mvn clean package

cd /home/test-parcel/target/repository
cp SGY* /opt/cloudera/parcel-repo/	#把.parcel和.parcel.sha复制到/parcel-repo
# 把/target/manifest.json的内容追加到/parcel-repo/manifest.json
#修改属主为cloudera-scm
```

打开CM点右上角的包裹图标，可以看见刚刚构建的包，分配、激活

**换parcel大包的时候要修改版本号**



## CSD

修改test-csd里pom.xml	test换为sgy

```shell
vim /home/test-csd/src/main/resources/service.sdl
# 修改了第2，3，4，7，8，11，55，56，58，158行		test_name改为test_sgy
# 78行loig改为log
cd /home/test-csd/src/main/image	# 替换图标（16*16px）
vim /home/test-csd/src/main/resources/scripts/control.sh
# 修改了第46，48，54，56，62行	！62行不改

cd /home/test-csd/
mvn clean package
#把/home/test-parcel/target里的jar包放到/opy/cloudera/csd
cp /home/test-csd/target/SGY-0.1.jar /opt/cloudera/csd/
# 修改jar包的属主为cloudera-scm
# 重启CM Server, 把对应机器上/var/log/sgy属主改成sgy:sgy
# CMservice实例 如果显示已过期配置，从下往上重启
# 进入界面http://namenode-1:7180/cmf/csd/refresh	搜sgy 找到installed：true，说明csd修改成功
```



## 添加配置项

修改/home/test-csd/src/main/resources/descriptor/service.sdl	（配置分公有和私有）

在"parameters"添加一段，如何最下方"configWriter"的"includedParams"里添加刚刚新建的name

```json
{  
    "name" : "a_config",		//不对外显示，添加到configWriter
    "label" : "PeiZhi_test",	//上面大字
    "description" : "lalala",	//点问号显示这个
    "configName" : "peizhi",	//下面小字
    "type" : "string",			// 还有memory uri path
    "default": "hello_word"		//默认值
},

"configWriter" : {
    "generators" : [
        {
            "filename" : "etc/sgy1_config.properties",
            "configFormat" : "properties",
            "includedParams" : [
                "sgy_name",
                "hive_metastore_uri",
                "log_dir",
                "sgy1_zkhosts",a
                "max_heap_size",
                "role_name",
                "a_config"
            ]
```

重新生成csd的jar包，上传，重启CMserver，重启本服务



### 参数解释

`test-csd/src/main/resources/descriptor/service. sdl`中的commands 相当于 声明

关联到t1_init就是此命令的    实现

关联的名字preStartSteps     使用



parameters里面的:

name 配置文件内部使用的名字，不对外

label CM界面上显示的名字 (上面的比较大的名字)



configName CM界面上显示的下方的那个名字

default  CM界面上的默认回显

roles外面的parameter是角色公用的配置参数

roles里面的parameter是该角色独有的



### 路径

**配置下发路径(带随机数) CONF_DIR: **

`/run/cloudera-scm-agent/process/` 		****

CM界面修改的配置, 放在 `/run/cloudera-scm-agent/process/` 里 以序号-服务名-角色名 命名的目录

**安装路径 CDH_TEST_HOME :**

`/opt/cloudera/parcels/TEST`			****		(TEST是服务名, 已改为SGY)	

**如何统一上面两个路径：**

如果应用程序安装目录下没有配置文件，就将应用程序下的bin目录软链接到配置下发目录下

如果应用程序目录下存在配置文件，就需要将配置下发目录下的配置文件修改到应用程序目录下的配置文件中.

![image-20220222194757021](C:\Users\sgy11\AppData\Roaming\Typora\typora-user-images\image-20220222194757021.png)

**配置生效三步曲: **

1. CM 界面修改的配置，重启服务，跳到 service.sdl
2. 从 service.sdl 跳到 scripts/control.sh
3. 从 control.sh 跳到应用程序



# 七. 第三方服务内嵌到CM

## CMAK

### 手动执行CMAK: 

```shell
./bin/cmak -Dhttp.port=9999 -java-home /usr/java/jdk-11.0.14
```



### CMAK嵌入CM

文件结构:

```shell
[root@namenode-1 cmak-parcel]# tree
.	# parcel生成.parcel和.sha
├── make_manifest.py	# 负责生成校验文件
├── packages			# 里面放要被CM管理的应用程序
│   └── cmak_sgy-3.0.0.3.zip
├── pom.xml		# 指定package里要解压的文件,mvn依赖,在本目录执行 mvn clean package 
├── README.md
└── src
    └── main
        └── resources
            ├── assemble.sh	# 指定解压命令(unzip或tar -zxvf),修改权限
            └── meta 		# 里面的四个文件,官方要求必须有 
                ├── alternatives.json 	# 路径
                ├── cmak_sgy_env.sh		# CDH_XXX_HOME:应用程序安装路径
                ├── parcel.json			# 路径
                └── permissions.json	# 空


[root@namenode-1 cmak-csd]# tree
.	# csd生成jar包,在http://namenode-1:7180/cmf/csd/refresh查看jar包是否有误
├── pom.xml		# mvn依赖,在本文件所在目录执行 mvn clean package 
├── README.md
└── src
    └── main
        └── resources
            ├── descriptor
            │   └── service.sdl	# 指定参数，角色，命令，类json格式
            ├── images
            │   └── icon.png	# 图标
            └── scripts
                └── control.sh	# 加载配置文件，初始化、启动服务
```

`vim /home/desktop/cmak-parcel/src/main/resources/assemble.sh`

里面cp命令后面加一条:  `chomd -R 777 ${parcel_name}`

**service.sdl 管配置项, 依赖			control.sh 管启动过程**

## Presto





## 总结

#### 坑: 

权限 (Permission denied)

端口占用 (Address already in use)

sdl语法错误 (jar包有问题,添加服务时找不到, 看http://namenode-1:7180/cmf/csd/refresh)

jdk版本

软件bug (每次上传parcel大包必须换版本号)

#### 解决方法:

 **提前看好文档, 出错看日志**





