# 00 万能帮助命令

`man`	手册

`type`	查命令是内置还是外置

`help cd`	内置命令这么用help

`ls --help`	外置命令这么用help

`info ls`	比help详细

# 01 目录操作

`ls`	list

-l	long 长格式

如：`drwxr-xr-x. 2 sgy sgy 6 Jan  3 21:02 Downloads`

| d           | rwx          | r-x        | r-x      | 2        | sgy      | sgy        | 6                      | Jan  3 21:02 | Downloads |
| ----------- | ------------ | ---------- | -------- | -------- | -------- | ---------- | ---------------------- | ------------ | --------- |
| d目录 -文件 | 当前用户权限 | 当前用户组 | 其他用户 | 硬链接数 | 所属用户 | 所属用户组 | 大小（不包括内部文件） | 修改时间     | 文件名    |

-a	隐藏文件

-A	除了.和..外 的隐藏文件

-ld	当前目录的属性

-t 	按时间顺序

-r	reverse逆序

-h	人性化显示文件大小

`ls -lrth`	此方法显示的目录大小，仅指目录本身，不包含目录内部文件大小

`data`	时间

`cal`	日历

`pwd` print working directory

`cd -`	返回跳转前的目录

### mkdir -p	级联创建目录

### cp	复制

-r	recursion 递归 复制目录 

**-p	保留文件或目录的属性（如修改时间）**

mv	移动 不需要参数 移动的同时可以改名

# 02 文件操作

touch	创建空文件

cat	显示文件内容		-n 行号	-E 在行尾加$显示	-A 显示各种看不见的字符如 table 变成 ^I

more	+5 从第5行开始显示	-5 每页显示5行	-p 清空再显示

less -N 行号	-p后面加单词

head	-n	前n行

tail -f 动态显示文件尾部,简写为 tailf

### ln

ln -s	软链接 相当于快捷方式 **inode不一样**

ln	硬链接 **inode一样** 硬链接数大于1时删除其中一个，磁盘不变化

### vim

**默认** 普通模式

**编辑模式**	I 行首	A 行尾	O 上一行	**i 光标前**	a 光标后	o 下一行

**命令模式**	:

**显示行号**	:set nu

**撤销**	u

**按行拆分文件**	ctrl+r

**返回第一行**	gg

**最后一行**	G

**跳到第20行** 20G

**向下移动5行**	5+回车	

**按字符移动光标** 数字＋上下左右hjkl

**搜索**	/	按n向后 N向前，大小写敏感 

**忽略大小写** 	:set ic	ignore case

**删除**	dd	d10d	删一行，删10行

**复制**	yy	y10y	复制一行，复制10行

**粘贴**	p

### echo

<a>></a>	覆盖重定向

<a>>></a>	追加重定向

**-e	激活转义字符**

### cut	（不如awk好用）

按列取

-d	" "	分隔符

-f3	第三个字段

### split

-l	按行拆分文件

-b	按大小拆分文件

-d	用数字编号（默认是字母）

### read

读取键盘输入的单行

```shell
read -p “please input your name: ” var
echo $var
```



# 03 用户和用户组操作

etc	法语 等等...		用于放配置文件

`cat /etc/group`	查看用户组

`groupadd`	创建一个用户组

`groupdel`	删除一个用户组

`groupmod -n newname oldname`	修改，GID不变

`cat /etc/passwd`	查看用户	UID:GID	（密码存在/etc/shadow）

`useradd`	创建一个新用户，若未指定用户组，自动创建同名组，新建的用户没有密码

**`useradd username -g groupname -d 家目录 -s `**	

**`userdel -r`  删除用户和家目录**

**`usermod -s /bin/nologin `	修改访问shell的方式，禁止用su切换到本用户**

`su - username`	切换用户（同时切换环境变量）$ 普通用户	# root用户

`passwd`	修改当前用户密码

`passwd username`	改别人密码，只有root用户能执行

root用户用su切换用户不需要输密码

用一次exit只能退出一层

**`pwck`	检查/etc/passwd**

**`grpck`	检查/etc/group**

**给普通用户root权限：**

修改	`/etc/sudoer`	该文件默认只有r权限，最好不改权限，用wq！

修改之后普通用户输入 `sudo -i` 命令就能变成root

**who/w	查看已连接用户**



# 04 权限操作

`chmod -R`	递归修改权限

`chown -R`	递归修改所属于用户

`chown root:root`	把用户和用户组改成root

`chgrp -R`	改所属组

( chown用户和组都能改，chgrp只能改组 )

# 05 压缩和解压

### tar

-zcvf	压缩	先压缩后文件名，后选择文件

-zxvf	解压

```shell
tar -zcvf 123.tar.gz a.txt b/		//把文件a和目录b压到123里面
```

解压zip文件：unzip



# 06 搜索

### find

**搜索文件名字，不能搜索内容**

`-name ./ " "`

`-iname`	不区分大小写，文件和目录都能查

`-perm`	按权限查

`-user`	按文件所属用户

`-type`	按文件类型 d / f

`-size +20M -100M`	// 大于20M	小于100M的文件

`-mmin n`	// n分钟之内改过的

`-mtime +5 -5`	**// 加号5天以前	减号近5天以内**

**标配：** **-exec** 对查找结果要执行的命令 **{} \;**

**`find ./ -type f -mtime +10 -exec rm -rf {} \;`		**

**等价于	**

**`find ./ -type f -mtime +10 | xargs -L rm -rf`**

-ok	类似exec, 需要再敲yes

`find ./ -name '[a-z]*[0-9].log'`  //字母开头数字结尾的log文件

### which

在**$PATH**里查找**可执行文件**所在位置	如 `which cat`

### whereis

查看可执行文件，比**which**结果多，只能用于程序名的搜索



# 07 文本处理

### grep 全局正则表达式搜索

搜索字符串匹配的行，默认在当前目录搜索

**-o		只显示匹配的单词而不是整行**

-A n	显示搜索结果及其**后面**n行

-B n	显示搜索结果及其**前面**n行

-C n	显示搜索结果及其**前后各n行**

-c		统计行数

**-E		扩展正则**

-n 显示匹配内容的所在文件中行数

**-w	完全匹配**	(word)		比如查端口号22

-c	只统计行数

**-i	忽略大小写**

-l	只列出文件内容符合指定的样式的文件名称

**-R 递归查找文件夹**

**-v 排除符合条件的，显示剩下的**

**`grep -Rni xxx./` 最常用，搜空格要加双引号**

`grep ^$`		匹配空行

.	任意一个字符

\*	前一个字符连续出现0至多次

.*	在正则里表示所有内容（任意字符出现任意次），相当于通配符*

### 正则表达式

^abc	以abc开始的行

abc$	以abc结尾的行

.			任意一个字符，不能没有字符

\*			匹配零个或多个先前字符   如 'a*grep' 可以匹配   **grep	agrep	aagrep	aaagrep** ...

.*			表示**任意个任意字符**，相当于find里的通配符*

[]			从中括号里面选一个	如 [abc] 表示 a 或 b 或 c 		[0-9]	表示0到9

[^]			除了中括号里面的			`[^abc]`匹配abc以外的内容

[a-zA-Z0-9]	大小写字母和数字，**中括号里面的符号一般不带特殊含义，如[a-z|A-Z,0-9]表示带|和,**

'[.]$' 等价于 '[\\.$]'

**正则的贪婪性(会尽量往后匹配)**

### 扩展正则

?	零次或一次

\+	出现一次或多次

|	或

()	限制范围

{n,m}	出现m到n次

### sed

stream editor **流编辑器**，可以不打开文件就对其做修改

特点：

执行过程：找谁干啥：找到某一行，对其增删改查

核心应用：增删改查	s	p	d	cai

**-n	只显示匹配的行，一般都要加**

-r	扩展正则

### sed查找p（返回的都是整行）

| 查找格式           |                                      |
| ------------------ | ------------------------------------ |
| '3p'               | 查找第三行                           |
| '1,5p'             | 一到五行                             |
| '/123/p'           | 含有123的行（ /    /的里面支持正则） |
| '/10:00/,/11:00/p' | 含有10:00的行到含有11:00的行         |
| '3,$p'             | 第3行到最后，（$表示最后一行）       |

!	表示不显示,		如`sed -n '3!p' file.txt`		**显示第3行之外的行**

`sed -nr '/^$/!p' file.txt`		**不显示空行**

### sed查找 d(把查找的p换成d就行, 整行删除)

### sed增加 ( cai )

| 命令 |                |
| ---- | -------------- |
| c    | 替换本行       |
| a    | 追加, 行的后面 |
| i    | 插入, 行的前面 |

sed '$a 123' file.txt		在文件最后插入123（$表示最后）	

### sed替换	's///g' 中间三个啥都行

g	global	不加g默认只替换每行第一个符合的内容

sed 's#[0-9]##g' file.txt	把数字换成空（相当于删除）

**后向引用 ( 反向引用 ), 先保护再使用：前面括号里的 变为后面的\1 \2 ...**

例一	123456的34两边加上尖括号，变为12<34>56

`echo 123456 | sed -r 's#(34)#<\1>#g'`			小括号要用-r，后面的\1 表示第一个小括号

例二	把123_abc变成abc_123

`echo 123_abc | sed -r 's#(^.*)_(.*$)#\2_\1#g'`		\2在前\1在后, ^和$锚定了开头和结尾

例三	获取eth0网卡的ip

`ip a show eth0 | sed -n '3p' | sed -r 's#^.*t (.*)/.*$#\1#g'`

eth0的第三行, 以 .*t开头 以/.\*结尾的部分是\1





15	`sed -i '/bbb/r 2.txt' 1.txt`	将2的内容插入到1里**包含**bbb的行的后面





### sort

默认按ACSII码，升序；可以把多个文件合在一起排

-u	排序并去重

-r	按降序排

-o	输出到文件	`sort 1.txt -o 1.txt`

-n	按数字排序

-t	指定分隔符

-k	指定是第几段，写多个 -k 就有了优先级

-f	忽略大小写

### uniq

去重（只能去相邻行），只保留第一个；**解决方式：先sort再uniq**

**-c	count 在每行前面显示去重前的重复次数**

**-d	repeat 仅显示重复的行，并去重**

-D	仅显示重复的行，并不去重

-i	忽略大小写

-s	skip 跳过每行的前n个字符

**-u	只显示不重复的行**,删掉所有重复的行

-w	只比较前n个字符，前面可以加 -s，从跳过后的开始数

### sort+uniq

**大前提：1和2自己本身没有重复**

`sort 1.txt 2.txt | uniq -d`				得到1和2的交集，

`sort 1.txt 2.txt | uniq` 					得到1和2的并集且去重

`sort 1.txt 2.txt 2.txt | uniq -u`		得1-2（1和2的交集必出现3次，2必出现两次，都被-u删除，剩1-2）

`sort 1.txt 1.txt 2.txt | uniq -u`		得2-1（1和2的交集必出现3次，1必出现两次，都被-u删除，剩2-1）

### paste

合并两个文件，横着粘，有点诡异

-d	指定分隔符，如  -d  @

-s	每个文件变为一行

**竖着粘：cat 2.txt >> 1.txt		把1插到2后面**

### awk		'条件{操作}'

文本分析工具, 有强大的文本格式化能力, 逐行处理

大括号两边必须是单引号, 大括号里面的字符串要加双引号

-F 分隔符 本质是`-v FS=`		FS:字段分隔符		`-v OFS=:` 输出时的字段**分隔符设为冒号**

NF	变量个数, 可用于取最后一列 $NF

NR	行号,不用加$		如 NR>=5 $$ NR<=10

$0	代表每一列



#### 例一	取出passwd的第一列和最后一列

`awk -F: '{print $1,$NF}' passwd | column -t`

#### 例二	交换passwd的第一列和最后一列

` awk -F: -vOFS=: '{print $NF,$2,$3,$4,$5,$6,$1}' passwd`

#### 例三	取出网卡ip

`ip a show eth0 | awk -F"[ /]+" 'NR==3{print $3}'`

指定空格和/都为分隔符, 因为开头时空格,所以第一列为空, 取$3而不是$2

#### **例四 批量kill进程**

`ps -ef |grep airflow|grep -v grep |awk '{print $2}' | xargs kill -9`

### awk精确到列再用正则:

awk -F: '$3~/^abc/'	表示以abc开头的列		~包含	!~不包含

例一	找出第三列以2开头的且显示第1列,第3列,最后一列

`awk -F: '$3~/^2/{print $1,$3,$NF}' passwd`

例二	统计空行 (i++)

`awk '/^$/{i++}END{print i}' file.txt`

例三	累加 (sum=sum+i)

`seq 100 | awk '{sum+=$1}END{print sum}'`

例四	遍历数组	都在BEGIN里，for i获取的是下标

`awk 'BEGIN{a[0]="abc";a[1]=123;for(i in a)print a[i]}'`

### 三剑客

| 命令 | 特点                 | 场景                                 |
| ---- | -------------------- | ------------------------------------ |
| grep | 过滤                 | 速度最快                             |
| sed  | 替换，修改文件，取行 | 替换、修改、取出某个**范围**的内容   |
| awk  | 取列，统计计算       | 取列，对比，比较，统计、计算（数组） |

### **扩展：tr 和 xargs**





# 08 进程相关

### ps

/proc 目录里面有PID

-ef	UID PID PPID

-aux	能看占用率

`ps -ef | grep mysqld | grep -v grep`

`grep -v grep`	排除grep

### top

任务管理器, 自动刷新

idle	空闲程度 应大于50%

%cpu	可以大于100, 每占满一个核心是100

free -h	查内存

# 09 系统信息

arch 查架构

uname -m	查架构

uname -r	linux内核版本

cat /proc/cpuinfo	cpu信息

cat /proc/version	linux内核版本

cat /etc/redhat-release	发行版本

date 系统时间

date格式化：date +"%Y-%m-%d %H:%M%S"   加号和引号中间没有空格

date -d "-180 days" +"%Y%m%d"		180天前

cal 年份 显示某年的日历

uptime	开机时间

last reboot	重启历史

**查看四大资源**

CPU: top

内存: free -h

硬盘: df -h

网络: 



# 10 磁盘相关

mkfs.ext4 /dev/sdb		格式化

mount	把磁盘挂载到某个目录

**df -h	人性化显示磁盘空间 **(在任意目录敲)

ls -lSr	按尺寸大小排序文件

du -sh dir1	看dir1总大小

**du -sh *	查看所有文件大小**

# 11 RPM包管理

Red-Hat Package Manager (RPM软件包管理器)

**yum install lrzsz 传文件**

**rz收文件 sz往Windows发文件**

**yum install epel-release**

**rpm -ivh** package.rpm	**安装**一个rpm包   i: install安装	v: verbose详细信息	h: hash哈希、散列

**rpm -Uvh** package.rpm	**更新**一个rpm包但不修改配置文件

rpm -qa	查看已安装的rpm包

rpm -e 卸载，包名为-qa查出来的名字

# 12 yum

全称：Yellow dog Updater, Modified

yum install package	下载并安装一个rpm包，自动处理依赖关系

yum update package	更新一个rpm包

yum remove package	删除一个rpm包

yum list	列出已安装的所有包

yum search package	在rpm仓库中搜寻软件包

**yum clean all	删除所有缓存的包和头文件**

`yum install epel-release`

# 13 文件格式转换

dos2unix 1.txt		将dos的文本转为unix的文本

unix2dos 1.txt		将unix的文本转为dos的文本

vim	:set ff		(file format)

# 14 网络

ip a

ifconfig ens33

loopback 回环 127.0.0.1

netmask 255.255.255.0

ifup eth0 启用一个网卡0

ifdown ens33	**禁用**网卡

>重启网卡固定用法：`ifdown ens33 ; ifup ens33`	否则断网没法输下一个命令
>
>重启网卡: service network restart
>
>**; 与 && 的区别**	
>
>&&	必须前面正常执行后面才执行
>
>;		无论前面是否正常执行，后面都能执行

### **netstat -nap | grep PID**		**查看某个进程监听的端口号**

mtr ip	My tracerout	`yum install mtr` 

### iftop				

`yum install iftop`

-i	指定网卡

-B	以Bytes为单位

-n	host默认显示ip

-N	使信息端口

-F	

-P	使host信息及端口信息默认都显示



ethtool ens32		查看网卡信息	其中Duplex应该是Full 全双工

**对于网络：1mb = 1000kb**

ifstat



# 15 磁盘IO

`iostat -x -d -k 1 100`

-d	单独输出device，不包括cpu

-k/-m	以KB/MB为单位

-x	更详细

1	采样间隔

100	采样次数

%util	数越大说明磁盘越忙

pidstat -d 1	查看哪个进程io高，d是时间间隔

# 16 JVM

查看java相关资源

# 17 文件传输

rz	从Windows收

sz	发到Windows

scp 123.txt root@192.168.10.131:/home		发送到远端

scp root@192.168.10.131:/home/123.txt /home	从远端拉取

















# 18 shell

## 0 易混淆

### $相关

**set**   设置变量 如 set a b c

$0	当前脚本程序的名字
$1	用户传入的第一个参数	(入参)
$HOME	当前用户的主目录
$#	用户传入的参数**总个数**
$*	用一个字符串显示所有参数	
$@	类似$*	每个参数单独一个字符串
$?	上一个命令的退出状态 (返回结果)，0为正常, 非0说明有错	(shell中0为真, 1为假)
\$$	shell的pid
$!	shell最后运行的程序的pid
${}	变量替换并连接, 同, echo $A	如	A=B	echo ${A}CD	结果是BCD
$[]	内部进行整数运算
$()	里面放命令，同双反引号	如 echo today is $(date)	或  echo today is \`date\`	
$(())	内部进行整数运算
$PATH 命令的搜索路径		pwd	等价于	/user/bin/pwd

**$PS1**	用来定义命令行的提示符

**$RANDOM**	用于生成0—32767的随机数



\`	\`	双反引号，里面放命令，同$()
'	'	双单引号，剥夺了所有字符的特殊含义
"	"	双引号，不去掉特殊含义

### ; 	 &&	||	 的区别

;		无论前面是否正常执行，后面都能执行

&&	必须前面正常执行后面才执行

||	左边命令返回为假 ($?==1), 才执行右边

​		左边返回值为真 ($? == 0), 右边不执行

### 执行命令的四种方式	(茴的四种写法)

```shell
./abc.sh			#直接执行,默认没有x权限
bash abc.sh			#用bash执行,无需x权限

# 前两个执行后不改变当前的目录(因为是起了一个子进程 子shell?)
# 后两个会留在shell执行过的目录(相当于直接在当前环境执行)

source ./abc.sh		#用source执行,无需x权限
. abc.sh			#用.执行,无需x权限
```

### 内建命令与外部命令

内建命令不需要常驻内存，创建子进程

内建命令仅对当前shell生效

用type命令查

两种传参   xxx.sh   var1	或		里面写read

### 查看当前系统的启动时间

who -b 

last reboot

w  

top 

uptime

### tr分词固定搭配

`tr -s ' ' '\n'`		-s 连续重复项只保留一个（对\n也起效）

### xargs

`cat file.txt | xargs`			列变行

`cat file.txt | xargs -n5`	行变列，每行5列

-d													指定分隔符

`-l`					后面跟命令，很强大	如	`ls | xargs -l chmod 644`		将ls查到的所有文件权限改为644

`-I`					`ls | xargs -I {} chomd 644 {}`								比`-l`好用，有两个{}填坑

### 目录不能创建硬链接



## 1 基本原则

一条命令只做一件事

不能乱加空格!	如 a=2 能写成 a = 2

\# !/bin/bash

cat的妙用:

单独的cat, 不加参数, 会一直等待键盘输入



/proc/PID/fd	file description文件描述符

0	标准输入	1	标准输出	2	错误输出		都软链接指向/dev/pts/1	(who命令查出的当前终端)	

### 重定向

echo 的输出必是正确输出也就是标准输出，默认输出到fd里1的号文件，也就是当前终端

所以 echo 123 在屏幕上回显123

**\>覆盖		\>\>追加		<输入重定向		<<分界符**

echo 123 > 1.txt		也就是echo 123 1> 1.txt	将原本指向当前屏幕的1号文件描述符(正确输出)重定向到1.txt	

echo 123 2> 2.txt	将2号文件描述符(错误输出)重定向到2.txt，由于echo没有错误，123还是显示到屏幕上

&>								正确错误都重定向



wc -l

等待输入, 按ctrl+d退出

wc -l < 1.txt	统计1.txt的行数



用脚本生成脚本

```sh
#!/bin/besh
#demo t7 土办法

echo "#!/bin/bash" > t8.sh
echo "# demo t8" >> t8.sh
echo "echo 123" >> t8.sh
echo "echo pwd" >> t8.sh
```

```sh
#!/bin/bash
#demo

cat > t9.sh <<EOF	#	> 可换位 >>	<<指定分界符
#!/bin/bash
# demo t9
echo 123
pwd
EOF	# 见到EOF就结束对cat的重定向

# 用cat读取 <<EOF 到 EOF 中间的语句,再保存到 t9;	EOF可任意换
```





## 2 变量

let a=10+20	不支持浮点型



变量的作用范围: 仅限当前shell	在 父shell,子shell,平行shell 都不管用



export		让父shell的变量可以被子shell用, 评选仍不管用

unset		取消赋值



## 3 四大配置文件

每个用户登录时都会加载这四个文件 (shell文件)

```shell
#全局
/etc/profile
/etc/bashrc
#当前用户
~/.bashrc
~/.bash_profile
```

su - sgy	切换用户且切换配置文件

su sgy	切换用户但配置文件加载不完全（少加载了~/home/sgy下的）

修改配置文件不会立即生效

需要：	`source /etc/profile`

## 修改主机名

[技术|Linux 中改变主机名的 4 种方法](https://linux.cn/article-10651-1.html)



## 数组

定义数组

array=(1 2 3)

显示所有元素

echo ${array[@]}

显示个数

echo ${#array[@]}

显示第0个(数组下标从0开始)

echo ${array{0}}	

## 转义与引用

字母前加反斜杠，转译成其他意思	如 `\t`

特殊符号前加反斜杠，转译成普通字符	如 `\$`

echo -e	使echo认识转义

```shell
echo -e "123\r456"	# \r 回车，回到当前行的行首，因此回显只有456
456

echo -e "123\n456"	# \n 其实是换行+回车
123
456

echo \$a
$a
```

""	不完全引用里面的$会变成取值符号

''	完全引用, 单引号里面是啥就回显啥



## 运算符

赋值	=

用unset取消赋值



expr	数字和符号中间要**有空格**, 乘法的*前面要加\

把3*4的结果存入a的正确用法：

```shell
a=`expr 3 \* 4`
#或者
a=$(expr 3 \* 4)
```



let

`let a=3+4`	（不能有空格）



(())	是let的简化

```shell
echo $((20+30))
50
```



## 测试与判断

### exit

exit 10	

$?	上一个命令的返回自值

### test

文件测试、整数比较测试、字符串测试

简写为	[ ]

[ ] 要有空格

[ ]的扩展写法	[ [ ] ]	&&	||	< >

```shell
# 比较a与b是否相等 的两种方法
test $a -eq $b
[ $a -eq $b ]
```

-e 判断存在	-f -d	判断文件or目录



### 拆分文件拓展名	basename

```shell
basename 123.txt .txt
123
```



类C语言格式

```sh
for(( i=1;i<=10;i++ ))
do
	echo $i
done
```



在shell里单独一行shift	可以去掉第一个用户入参

**shell里面的变量默认是全局变量!**

想用局部,要在函数里的变量前＋local

grep -w	完全匹配

系统内置函数, 使用前先source /etc/init.d/functions





```shell
wc -l
123
456
789
ctrl+d #提交
3
```



at	一次性计划任务

```shell
```



# 四 crontab

最小周期	1分钟

\* \* \* \* \*		分 时 日 月 周				5个*表示每分钟执行一次

时间格式：

星号（*）：代表所有可能的值，如month字段为星号，则表示在满足其它字段的制约条件后每月都执行

逗号（,）：可以用逗号隔开的值指定一个列表范围，例如，“1,2,5,7,8,9”

中杠（-）：可以用整数之间的中杠表示一个整数范围，例如“2-6”表示“2,3,4,5,6”

正斜线（/）：可以用正斜线指定时间的间隔频率，例如“*/2 * * * *”表示每两分钟执行一次。

文件保存位置：`/var/spool/cron`  

## flock 文件锁

应用场景: 备份脚本

作用:	保证只有一个实例正在运行

使用参数:

-x	exclusive 排他锁

-n	nonblock 如果锁不能背立即获取,直接报错而不是等待

-c	command 执行命令

`flock -xn "/tmp/lock001.lock" -c "/home/sgy/a.shell"`



# 五 nohup & exec

### nohup		no hang up 不挂断运行

默认输出到nohup.out

关闭session	发送signup信号, 程序免疫

Ctrl+c	发送sigint信号, 程序关闭

### &		后台运行

结果会输出到终端

Ctrl+c	发送sigint信号, 程序免疫

关闭session	发送signup信号, 程序关闭

nohup 命令 > aaa.log 2>&1 &

### exec

不产生新的子进程, 以新进程代替旧进程,但PID不变

脚本里调用其他脚本用, 可以少一级



# 六 初始化与快捷方式

### 初始化 （开机自启动）

在`/etc/rc.d/rc.local`里添加脚本的路径

`chmod  a+x /etc/rc.d/rc.local`

输出要写绝对路径

### **快捷方式**

**法一, 修改path**

export PATH=PATH:/home/sgy/xxx.sh

**法二, 创建软链接	(放到/usr/bin/cli就能直接执行)**

ln -s /home/sgy/xxx.sh /usr/bin/cli

**法三, 起别名**

在~/.bashrc中添加

`alias xxx='/home/sgy/xx.sh'`

如何source ~/.bashrc



# 七 supervisor进程管理工具

监管者

典型的C/S模型的程序	supervisord 是server端，supervisorctl 是client端

**自动拉起**

监控应用程序，崩溃后立刻拉起

创建一个能输出日志的sh

修改配置文件	/etc/supervisord.conf

启动supervisor	`/usr/bin/supervisord -c /etc/supervisord.conf`

supervisorctl	客户端,	status start stop



# 八 yum本地源

`createrepo /home/sgy/yum/`

在/etc/yum.repos.d创建文件	local.repo

```shell
[local-repo]
name=local-repo
#baseurl=http://192.168.11.116/repo/BDP/
baseurl=file:///home/sgy/yum
enabled=1
gpgcheck=0
```

这时 `yum list`就能查看到放在/home/sgy/yum文件夹里的rpm包

# 九 ssh免密

配置主机A免密登录到主机B

前提	~/.ssh/	文件夹为空

- 1 在主机A和主机B上，都执行如下操作，生成公钥
          ssh-keygen –t rsa
          一直回车，此时和在家目录下生成 .ssh目录

- 2 将主机A的 ~/.ssh/id_rsa.pub的内容，追加到主机B的~/.ssh/authorized_keys(没有此文件，就     新建) 

- 3 在主机B上：chmod 600 ~/.ssh/authorized_keys

- 4 在主机上A ssh 用户名@B，即可实现免密登录
          注意：用户名就是你1-3步中登录B使用的用户。



# 十 ntp配置

ntp配置-主节点
• NTP是网络时间协议(Network Time Protocol)

• 1 如若没有现成的ntp服务器，需要选择一个节点作为主节点，作为ntp服务器
     /etc/ntp.conf修改为如下配置
driftfile /var/lib/ntp/drift
restrict 127.0.0.1
restrict -6 ::1
restrict default nomodify notrap 
server   127.127.1.0     # local clock
fudge    127.127.1.0 stratum 10
includefile /etc/ntp/crypto/pw
keys /etc/ntp/keys

ntp配置-其他节点
• 2 主节点之外的其他节点配置，将server指向主节点
     /etc/ntp.conf修改为如下配置
server 127.127.1.0
fudge 127.127.1.0  stratum 10
driftfile /var/lib/ntp/drift/ntp.drift
logfile /var/log/ntp
keys /etc/ntp.keys
trustedkey 1
requestkey 1
server 192.168.10.131 iburst
restrict 192.168.10.131

其中 192.168.1.111为主节点的IP地址。

ntp配置-操作命令
• 手动与主节点进行时钟同步
ntpdate 192.168.1.111

• 启动ntp服务
service ntpd start

• 查看时钟同步状态
  ntpstat

• 查看ntp连接状态
   ntpq -p



# 十一 screen

应用场景：集群间的数据备份

screen -S yourname		创建一个会话

screen -ls							查看所有会话

screen -r session-id			恢复一个会话

ctrl+a+d								在一个会话里提交任务	

exit										退出一个会话







service + 服务名 + 动作

systemctl + 动作 + 服务名















