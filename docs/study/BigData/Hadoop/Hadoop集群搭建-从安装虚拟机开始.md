# Hadoop集群搭建

1. 安装虚拟化软件VMware
2. 准备3台linux虚拟机
3. 搭建3节点zookeeper集群
4. 搭建3节点的hadoop集群

> ### linux版本
>
> - linux统一使用centos7.6  64位版本
>- 种子文件下载地址：http://mirrors.aliyun.com/centos/7.6.1810/isos/x86_64/CentOS-7-x86_64-DVD-1810.torrent

## 1. 三台linux服务器的安装

### 1.1. 安装VMware

- VMware虚拟机软件是一个“虚拟[PC](https://baike.baidu.com/item/PC/107)”软件，它使你可以在一台机器上同时运行二个或更多[Windows](https://baike.baidu.com/item/Windows)、[DOS](https://baike.baidu.com/item/DOS/32025)、[LINUX](https://baike.baidu.com/item/LINUX)系统。与“多启动”系统相比，[VMWare](https://baike.baidu.com/item/VMWare)采用了完全不同的概念。

- 我们可以通过VMware来安装我们的linux虚拟机，然后通过linux虚拟机来进行集群的安装，VMware的安装双击之后，一路下一步即可，尽量不要装在操作系统盘里面了，VMware的安装步骤省略

  <img src="assets/vm.png" style="zoom:80%;" />

### 1.2. 通过Vmware安装第一台linux机器

- 我们通过Vmware可以安装第一台我们的linux机器，接下来我们来看如何通过VMWare创建linux虚拟机，并给我们的虚拟机挂载操作系统

1.2.1：双击Vmware打开之后，点击创建新的虚拟机

![](assets/1.png)

 

1.2.2：选择自定义安装配置

![](assets/2.png)

![image-20200513103314891](assets/image-20200513103314891.png)

 

1.2.3：选择稍后安装操作系统

![](assets/4.png)

 

1.2.4：选择稍后安装操作系统

![](assets/5.png)

 

1.2.5：选择安装路径，==尽量不要放在C盘，并且所在盘符的剩余空间尽量大些==

![image-20200513103635615](assets/image-20200513103635615.png)



1.2.6：CPU核数，默认即可

![](assets/7.png)

 

1.2.7：虚拟机内存根据自身windows电脑进行调整

例如如果windows是8GB内存，那么每台虚拟机内存给2048M内存，如果windows是16GB没存，那么每台虚拟机可以给3072M内存即可

![](assets/8.png)

 

1.2.8：网络配置一定要选择==NAT==

![](assets/9.png)

![](assets/10.png)

![](assets/11.png)



1.2.9：磁盘大小尽量给40GB

![](assets/12.png)

 

注意：千万==不要==勾选“立即分配所有磁盘空间”

![](assets/13.png)

![image-20200513104013911](assets/image-20200513104013911.png)

1.2.10：完成

![image-20200513104053651](assets/image-20200513104053651.png)



### 1.3. 为我们创建的linux虚拟机挂载操作系统

- 我们现在已经有了一台虚拟电脑了，就类似我们刚刚买了一台电脑回来，只不过不同的是我们这台虚拟电脑还没有操作系统我们需要为这台电脑挂在操作系统出来

1.3. 1：通过设置来挂载操作系统

![](assets/16.png)

![](assets/17.png)

![](assets/18.png)

1.3. 2：直接回车开始安装

用键盘的方向键，选中“Install CentOS 7”,然后按回车，开始安装

![image-20200715134015711](assets/image-20200715134015711.png)

再按回车键

![](assets/19.png)

1.3. 3：设置键盘为英文键盘

![image-20201021111652532](assets/image-20201021111652532.png)



1.3. 4：接下来配置这三项

![](assets/21.png)

（1）设置①时区为Asia/Shanghai

![](assets/22.png)

![image-20201021111858683](assets/image-20201021111858683.png)



（2）设置②INSTALATION DESTINATION

![image-20201021112000200](assets/image-20201021112000200.png)

![](assets/23.png)

（3）设置③NETWORK & HOST NAME



![image-20200513105009692](assets/image-20200513105009692.png)



1.3. 5：设置root用户密码

![](assets/26.png)

1.3. 6：安装完成之后重启reboot即可

此过程稍长，耐心等待

![image-20200513105644118](assets/image-20200513105644118.png)



### 1.4. 为我们的linux虚拟机设置网络配置

- 我们的linux虚拟机已经创建并挂载好了操作系统，接下来我们可以为我们的第一台虚拟机来设置网络地址了，设置网络地址比较麻烦，尽量**参见视频**进行一步步的操作

1.4.1：设置虚拟机的网段

![](assets/28.png)

1.4.2：查看==NAT模式==的网关，子网IP以及子网掩码

![image-20201019113303256](assets/image-20201019113303256.png)



1.4.3：设置window当中的VMNet8网络地址

![](assets/30.png)

![image-20201019114244290](assets/image-20201019114244290.png)



1.4.4：设置linux当中的网络

- 我们已经配置好了Vmware当中的网络、windows当中的网络；
- 剩下就是配置linux虚拟机当中的网络，配置好了linux当中的网络，我们的linux就可以联网使用了

- 登录linux

![](assets/32.png)

编辑配置文件

```shell
vi /etc/sysconfig/network-scripts/ifcfg-ens33
```

添加联网四要素

```properties
IPADDR=192.168.51.100
NETMASK=255.255.255.0
GATEWAY=192.168.51.1
DNS1=8.8.8.8
```

具体参考下图

![image-20201019114510503](assets/image-20201019114510503.png)



更改完成配置，重启网络服务

```shell
systemctl restart network
```

安装一些常用的软件

```shell
yum -y install vim
yum -y install net-tools
```

关机

```
init 0
```



### 1.5. 克隆第一台机器

- 现在我们已经有了种子机器了，我们可以通过种子机器进行复制或者克隆出三台机器

- 关闭linux种子机器，然后准备进行克隆

![](assets/34.png)

![image-20200513111624820](assets/image-20200513111624820.png)

![](assets/35.png)

选择创建完整克隆

![](assets/36.png)

![image-20200513111854167](assets/image-20200513111854167.png)



### 1.6. 更改克隆机器的IP地址

- 三台机器的ip地址分别是`192.168.51.100、192.168.51.110、192.168.51.120`

- 克隆出来的机器IP地址与种子的ip地址一样，我们将第二台机器的IP地址更改为192.168.51.110即可

- 启动虚拟机，并通过root用户，密码123456来进行登录，然后来更改linux机器的IP地址

```shell
vi /etc/sysconfig/network-scripts/ifcfg-ens33
```

```shell
IPADDR=192.168.51.110
NETMASK=255.255.255.0
GATEWAY=192.168.51.1
DNS1=8.8.8.8
```

- 依照上面步骤，接着克隆第三台机器，并将第三台机器的IP地址设置为

  192.168.51.120

<font color='red'>建议：三台机器准备好后，打个快照，便于出错后恢复</font>



## 2. 安装大数据集群前的环境准备

### 2.1. 三台虚拟机关闭防火墙

三台机器执行以下命令（<font color='red'>root</font>用户来执行）

```shell
systemctl stop firewalld
systemctl disable firewalld
```



### 2.2. 三台机器关闭selinux

三台机器执行以下命令关闭selinux

```shell
vi /etc/sysconfig/selinux
```

```shell
SELINUX=disabled
```



### 2.3. 三台机器更改主机名

三台机器执行以下命令更改主机名

```shell
vi /etc/hostname
```

第一台机器更改内容

```
node01.kaikeba.com
##我改的是 node01.huangqiang.com
```

第二台机器更改内容

```
node02.kaikeba.com
##我改的是 node02.huangqiang.com
```

第三台机器更改内容

```
node03.kaikeba.com
##我改的是 node03.huangqiang.com
```



### 2.4. 三台机器做主机名与IP地址的映射

三台机器执行以下命令更改主机名与IP地址的映射

```shell
vi /etc/hosts
```

 

```shell
192.168.51.100 node01.kaikeba.com node01
192.168.51.110 node02.kaikeba.com node02
192.168.51.120 node03.kaikeba.com node03

###我的是
192.168.230.100 node01.huangqiang.com node01
192.168.230.110 node02.huangqiang.com node02
192.168.230.120 node03.huangqiang.com node03
```

==注意：根据自己的实际情况，修改ip地址==

### 2.5. 三台机器时钟同步

##### 第一种同步方式：通过网络进行时钟同步

通过网络连接外网进行时钟同步,必须保证虚拟机连上外网

三台机器都安装ntpdate

```shell
yum -y install ntpdate
```

阿里云时钟同步服务器

```
ntpdate ntp4.aliyun.com
```

三台机器定时任务

```shell
crontab -e
```

添加如下内容

```shell
*/1 * * * * /usr/sbin/ntpdate ntp4.aliyun.com;
```

##### 第二种同步方式：内网某机器作为时钟同步服务器

<font color='red'>以下操作都在root用户下面执行，通过su root切换到root用户</font>

以192.168.51.100这台服务器的时间为准进行时钟同步

###### 第一步:三台机器确定是否安装了ntpd的服务

三台机器确认是否安装ntpdate时钟同步工具

```shell
rpm -qa | grep ntpdate
```

如果没有安装,三台机器执行以下命令可以进行在线安装

```shell
yum -y install ntpdate
```

安装后如下图

![](assets/38.png)

node01安装ntp

```shell
yum -y install ntp
```

三台机器，执行以下命令，设置时区为中国上海时区

```shell
timedatectl set-timezone Asia/Shanghai
```

###### 第二步：node01启动ntpd服务

我们需要启动node01的ntpd服务，作为服务端，对外提供同步时间的服务

启动ntpd的服务

```shell
#启动ntpd服务
systemctl start ntpd

#设置ntpd服务开机启动
systemctl enable ntpd
```

 

###### 第三步：修改node01服务器配置

修改node01这台服务器的时钟同步配置，允许对外提供服务

```shell
vim /etc/ntp.conf
```

<font color='red'>添加以下两行内容</font>

```shell
# 同意192.168.51.0网段（修改成自己的网段）的所有机器与node01同步时间
restrict 192.168.51.0 mask 255.255.255.0 nomodify notrap
server 127.127.1.0
```

<font color='red'>注释掉以下这四行内容</font>

```shell
#server 0.centos.pool.ntp.org iburst
#server 1.centos.pool.ntp.org iburst
#server 2.centos.pool.ntp.org iburst
#server 3.centos.pool.ntp.org iburst
```

 ![](assets/39.png)

 

修改完成之后，重启node01的ntpd服务

```shell
systemctl restart ntpd
```

至此，ntpd的服务端已经安装配置完成，接下来配置客户端与服务端进行同步



###### 第四步：配置node02与node03同步node01的时间

客户端node02与node03设置时区与node01保持一致Asia/Shanghai

node02与node03修改配置文件，保证每次时间写入硬件时钟

```shell
vim /etc/sysconfig/ntpdate
```

```shell
SYNC_HWCLOCK=yes
```

node02与node03修改定时任务，定时与node01同步时间

```shell
[root@node03 hadoop]# crontab -e
```

 增加如下内容

```shell
*/1 * * * * /usr/sbin/ntpdate node01
```

 

### 2.6. 三台机器添加普通用户

三台linux服务器统一添加普通用户hadoop，并给以sudo权限，用于以后所有的大数据软件的安装

并统一设置普通用户的密码为 ==123456==

```shell
useradd hadoop
passwd hadoop
```

普通用户的密码设置为123456

三台机器为普通用户添加sudo权限

```shell
visudo
```

 增加如下内容

```shell
hadoop ALL=(ALL)    ALL
```

 

### 2.7. 三台定义统一目录

定义三台linux服务器软件压缩包存放目录，以及解压后安装目录，三台机器执行以下命令，创建两个文件夹，一个用于存放软件压缩包目录，一个用于存放解压后目录

```shell
mkdir -p /kkb/soft   # 软件压缩包存放目录
mkdir -p /kkb/install # 软件解压后存放目录
chown -R hadoop:hadoop /kkb  # 将文件夹权限更改为hadoop用户

###我的是
mkdir -p /hq/soft   # 软件压缩包存放目录
mkdir -p /hq/install # 软件解压后存放目录
chown -R hadoop:hadoop /hq  # 将文件夹权限更改为hadoop用户
```

 

<font color='red'>创建hadoop用户之后，我们三台机器都通过hadoop用户来进行操作，以后再也不需要使用root用户来操作了</font>

<font color='red'>三台机器通过 su hadoop命令来切换到hadoop用户</font>

```shell
su hadoop
```

```
###我的注意事项
###finalshell创建完3个服务器hadoop用户的ssh连接后，连上之后，输入下面命令，重启主机，然后等服务器重启完之后，再用这个hadoop用户的shh连接，连接上之后@后面就是node01 02 03了
sudo init 6

```



### 2.8. 三台机器hadoop用户免密码登录

重启下3个linux虚拟机，让主机名生效

第一步：三台机器在<font color='red'>hadoop</font>用户下执行以下命令生成公钥与私钥

```shell
ssh-keygen -t rsa

#会在/home/hadoop目录下生成一个.ssh的目录
```

<font color='red'>执行上述命令之后，按三次Enter键即可生成了</font>

 

第二步：三台机器在hadoop用户下，执行命令拷贝公钥到node01服务器

```shell
ssh-copy-id node01
```

 

第三步：node01服务器将公钥拷贝给node02与node03

node01在hadoop用户下，执行以下命令，将authorized_keys拷贝到node02与node03服务器

```shell
cd /home/hadoop/.ssh/
scp authorized_keys node02:$PWD
scp authorized_keys node03:$PWD
```

 

第四步：验证；从任意节点是否能免秘钥登陆其他节点；如node01免密登陆node02

```sh
ssh node02
```



### 2.9. 三台机器关机重启

三台机器在hadoop用户下执行以下命令，实现关机重启

```shell
sudo reboot -h now
```



### 2.10. 三台机器安装jdk

- 使用hadoop用户来重新连接三台机器，然后使用hadoop用户来安装jdk软件

- 上传压缩包到第一台服务器的/kkb/soft下面，然后进行解压，配置环境变量即可，三台机器都依次安装即可

```shell
cd /kkb/soft/
tar -xzvf jdk-8u141-linux-x64.tar.gz -C /kkb/install/
sudo vim /etc/profile

###我的是
cd /hq/soft/
tar -xzvf jdk-8u141-linux-x64.tar.gz -C /hq/install/
sudo vim /etc/profile

###解压完一个也可以scp发送到其他主机上
scp -r jdk1.8.0_141/ node02:$PWD
scp -r jdk1.8.0_141/ node03:$PWD
```

 

```shell
#添加以下配置内容，配置jdk环境变量
export JAVA_HOME=/kkb/install/jdk1.8.0_141
export PATH=$PATH:$JAVA_HOME/bin

###我的是
#添加以下配置内容，配置jdk环境变量
export JAVA_HOME=/hq/install/jdk1.8.0_141
export PATH=$PATH:$JAVA_HOME/bin
```

让修改马上生效

```shell
source /etc/profile
```

<font color='red'>建议：三台机器准备好后，打个快照，便于出错后恢复</font>



## 3. hadoop集群的安装

- 安装环境服务部署规划

| 服务器IP       | node01            | node02      | node03      |
| -------------- | ----------------- | ----------- | ----------- |
| HDFS           | NameNode          |             |             |
| HDFS           | SecondaryNameNode |             |             |
| HDFS           | DataNode          | DataNode    | DataNode    |
| YARN           | ResourceManager   |             |             |
| YARN           | NodeManager       | NodeManager | NodeManager |
| 历史日志服务器 | JobHistoryServer  |             |             |

### 3.1. 第一步：上传压缩包并解压

- 将我们重新编译之后支持snappy压缩的hadoop包上传到第一台服务器并解压；第一台机器执行以下命令

```shell
cd /kkb/soft/
tar -xzvf hadoop-3.1.4.tar.gz -C /kkb/install

###我的是
cd /hq/soft/
tar -xzvf hadoop-3.1.4.tar.gz -C /hq/install
```



### 3.2.第二步：查看hadoop支持的压缩方式以及本地库

第一台机器执行以下命令

```shell
cd /kkb/install/hadoop-3.1.4/
bin/hadoop checknative

###我的是
cd /hq/install/hadoop-3.1.4/
bin/hadoop checknative
```

<img src="assets/image-20201027152059863.png" alt="image-20201027152059863" style="zoom: 50%;" />

如果出现openssl为false，那么==所有机器==在线安装openssl即可，执行以下命令，虚拟机联网之后就可以在线进行安装了

```shell
sudo yum -y install openssl-devel
```



### 3.3.第三步：修改配置文件

#### 3.3.1.修改hadoop-env.sh

第一台机器执行以下命令

```shell
cd /kkb/install/hadoop-3.1.4/etc/hadoop/
vim hadoop-env.sh

###我的是
cd /hq/install/hadoop-3.1.4/etc/hadoop/
vim hadoop-env.sh
```

 

```shell
export JAVA_HOME=/kkb/install/jdk1.8.0_141

###我的是
export JAVA_HOME=/hq/install/jdk1.8.0_141
```

#### 3.3.2.修改core-site.xml

第一台机器执行以下命令

```shell
vim core-site.xml
```

 

```xml
<configuration>
   <property>
        <name>fs.defaultFS</name>
        <value>hdfs://node01:8020</value>
    </property>
    <property>
        <name>hadoop.tmp.dir</name>
        <value>/kkb/install/hadoop-3.1.4/hadoopDatas/tempDatas</value>
    </property>
    <!--  缓冲区大小，实际工作中根据服务器性能动态调整；默认值4096 -->
    <property>
        <name>io.file.buffer.size</name>
        <value>4096</value>
    </property>
    <!--  开启hdfs的垃圾桶机制，删除掉的数据可以从垃圾桶中回收，单位分钟；默认值0 -->
    <property>
        <name>fs.trash.interval</name>
        <value>10080</value>
    </property>

 <property>
        <name>hadoop.proxyuser.hadoop.hosts</name>
        <value>*</value>
    </property>
    <property>
        <name>hadoop.proxyuser.hadoop.groups</name>
        <value>*</value>
    </property>
    <property>
        <name>hadoop.http.staticuser.user</name>
        <value>hadoop</value>
    </property>
</configuration>


###我的是
<configuration>
   <property>
        <name>fs.defaultFS</name>
        <value>hdfs://node01:8020</value>
    </property>
    <property>
        <name>hadoop.tmp.dir</name>
        <value>/hq/install/hadoop-3.1.4/hadoopDatas/tempDatas</value>
    </property>
    <!--  缓冲区大小，实际工作中根据服务器性能动态调整；默认值4096 -->
    <property>
        <name>io.file.buffer.size</name>
        <value>4096</value>
    </property>
    <!--  开启hdfs的垃圾桶机制，删除掉的数据可以从垃圾桶中回收，单位分钟；默认值0 -->
    <property>
        <name>fs.trash.interval</name>
        <value>10080</value>
    </property>

 <property>
        <name>hadoop.proxyuser.hadoop.hosts</name>
        <value>*</value>
    </property>
    <property>
        <name>hadoop.proxyuser.hadoop.groups</name>
        <value>*</value>
    </property>
    <property>
        <name>hadoop.http.staticuser.user</name>
        <value>hadoop</value>
    </property>
</configuration>
```



#### 3.3.3.修改hdfs-site.xml

第一台机器执行以下命令

```shell
vim hdfs-site.xml
```

```xml
<configuration>
     <property>
            <name>dfs.namenode.secondary.http-address</name>
            <value>node01:9868</value>
    </property>
    <property>
        <name>dfs.namenode.http-address</name>
        <value>node01:9870</value>
    </property>
    <!-- namenode保存fsimage的路径 -->
    <property>
        <name>dfs.namenode.name.dir</name>
        <value>file:///kkb/install/hadoop-3.1.4/hadoopDatas/namenodeDatas</value>
    </property>
    <!--  定义dataNode数据存储的节点位置，实际工作中，一般先确定磁盘的挂载目录，然后多个目录用，进行分割  -->
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>file:///kkb/install/hadoop-3.1.4/hadoopDatas/datanodeDatas</value>
    </property>
    <!-- namenode保存editslog的目录 -->
    <property>
        <name>dfs.namenode.edits.dir</name>
        <value>file:///kkb/install/hadoop-3.1.4/hadoopDatas/dfs/nn/edits</value>
    </property>
    <!-- secondarynamenode保存待合并的fsimage -->
    <property>
        <name>dfs.namenode.checkpoint.dir</name>
        <value>file:///kkb/install/hadoop-3.1.4/hadoopDatas/dfs/snn/name</value>
    </property>
    <!-- secondarynamenode保存待合并的editslog -->
    <property>
        <name>dfs.namenode.checkpoint.edits.dir</name>
        <value>file:///kkb/install/hadoop-3.1.4/hadoopDatas/dfs/nn/snn/edits</value>
    </property>
    <property>
        <name>dfs.replication</name>
        <value>3</value>
    </property>
    <property>
        <name>dfs.permissions.enabled</name>
        <value>false</value>
    </property>
	<property>
        <name>dfs.blocksize</name>
        <value>134217728</value>
    </property>
</configuration>


###我的是
<configuration>
     <property>
            <name>dfs.namenode.secondary.http-address</name>
            <value>node01:9868</value>
    </property>
    <property>
        <name>dfs.namenode.http-address</name>
        <value>node01:9870</value>
    </property>
    <!-- namenode保存fsimage的路径 -->
    <property>
        <name>dfs.namenode.name.dir</name>
        <value>file:///hq/install/hadoop-3.1.4/hadoopDatas/namenodeDatas</value>
    </property>
    <!--  定义dataNode数据存储的节点位置，实际工作中，一般先确定磁盘的挂载目录，然后多个目录用，进行分割  -->
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>file:///hq/install/hadoop-3.1.4/hadoopDatas/datanodeDatas</value>
    </property>
    <!-- namenode保存editslog的目录 -->
    <property>
        <name>dfs.namenode.edits.dir</name>
        <value>file:///hq/install/hadoop-3.1.4/hadoopDatas/dfs/nn/edits</value>
    </property>
    <!-- secondarynamenode保存待合并的fsimage -->
    <property>
        <name>dfs.namenode.checkpoint.dir</name>
        <value>file:///hq/install/hadoop-3.1.4/hadoopDatas/dfs/snn/name</value>
    </property>
    <!-- secondarynamenode保存待合并的editslog -->
    <property>
        <name>dfs.namenode.checkpoint.edits.dir</name>
        <value>file:///hq/install/hadoop-3.1.4/hadoopDatas/dfs/nn/snn/edits</value>
    </property>
    <property>
        <name>dfs.replication</name>
        <value>3</value>
    </property>
    <property>
        <name>dfs.permissions.enabled</name>
        <value>false</value>
    </property>
	<property>
        <name>dfs.blocksize</name>
        <value>134217728</value>
    </property>
</configuration>
```



#### 3.3.4.修改mapred-site.xml

第一台机器执行以下命令

```shell
vim mapred-site.xml
```

```xml
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
    <property>
        <name>mapreduce.job.ubertask.enable</name>
        <value>true</value>
    </property>
    <property>
        <name>mapreduce.jobhistory.address</name>
        <value>node01:10020</value>
    </property>
    <property>
        <name>mapreduce.jobhistory.webapp.address</name>
        <value>node01:19888</value>
    </property>
        <property>
        <name>yarn.app.mapreduce.am.env</name>
        <value>HADOOP_MAPRED_HOME=${HADOOP_HOME}</value>
    </property>
    <property>
        <name>mapreduce.map.env</name>
        <value>HADOOP_MAPRED_HOME=${HADOOP_HOME}</value>
    </property>
    <property>
        <name>mapreduce.reduce.env</name>
        <value>HADOOP_MAPRED_HOME=${HADOOP_HOME}</value>
    </property>
</configuration>
```



#### 3.3.4.修改yarn-site.xml

第一台机器执行以下命令

```shell
vim yarn-site.xml
```

```xml
<configuration>
   
<property>
       <name>yarn.resourcemanager.hostname</name>
        <value>node01</value>
    </property>
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>

     <property>
        <name>yarn.nodemanager.env-whitelist</name>
        <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
    </property>
    <property>
        <name>yarn.scheduler.minimum-allocation-mb</name>
        <value>512</value>
    </property>
    <property>
        <name>yarn.scheduler.maximum-allocation-mb</name>
        <value>4096</value>
    </property>
    <property>
        <name>yarn.nodemanager.resource.memory-mb</name>
        <value>4096</value>
    </property>
    <property>
        <name>yarn.nodemanager.pmem-check-enabled</name>
        <value>false</value>
    </property>
    <property>
        <name>yarn.nodemanager.vmem-check-enabled</name>
        <value>false</value>
    </property>
<property>
        <name>yarn.log-aggregation-enable</name>
        <value>true</value>
</property>
	<property>
        	<name>yarn.log.server.url</name>
        	<value>http://node01:19888/jobhistory/logs</value>
	</property>
	<property>
        	<name>yarn.log-aggregation.retain-seconds</name>
        	<value>25920000</value>
	</property>

</configuration>


###我的是
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
    <property>
        <name>mapreduce.job.ubertask.enable</name>
        <value>true</value>
    </property>
    <property>
        <name>mapreduce.jobhistory.address</name>
        <value>node01:10020</value>
    </property>
    <property>
        <name>mapreduce.jobhistory.webapp.address</name>
        <value>node01:19888</value>
    </property>
        <property>
        <name>yarn.app.mapreduce.am.env</name>
        <value>HADOOP_MAPRED_HOME=/hq/install/hadoop-3.1.4/etc/hadoop:/hq/install/hadoop-3.1.4/share/hadoop/common/lib/*:/hq/install/hadoop-3.1.4/share/hadoop/common/*:/hq/install/hadoop-3.1.4/share/hadoop/hdfs:/hq/install/hadoop-3.1.4/share/hadoop/hdfs/lib/*:/hq/install/hadoop-3.1.4/share/hadoop/hdfs/*:/hq/install/hadoop-3.1.4/share/hadoop/mapreduce/lib/*:/hq/install/hadoop-3.1.4/share/hadoop/mapreduce/*:/hq/install/hadoop-3.1.4/share/hadoop/yarn:/hq/install/hadoop-3.1.4/share/hadoop/yarn/lib/*:/hq/install/hadoop-3.1.4/share/hadoop/yarn/*</value>
    </property>
    <property>
        <name>mapreduce.map.env</name>
        <value>HADOOP_MAPRED_HOME=/hq/install/hadoop-3.1.4/etc/hadoop:/hq/install/hadoop-3.1.4/share/hadoop/common/lib/*:/hq/install/hadoop-3.1.4/share/hadoop/common/*:/hq/install/hadoop-3.1.4/share/hadoop/hdfs:/hq/install/hadoop-3.1.4/share/hadoop/hdfs/lib/*:/hq/install/hadoop-3.1.4/share/hadoop/hdfs/*:/hq/install/hadoop-3.1.4/share/hadoop/mapreduce/lib/*:/hq/install/hadoop-3.1.4/share/hadoop/mapreduce/*:/hq/install/hadoop-3.1.4/share/hadoop/yarn:/hq/install/hadoop-3.1.4/share/hadoop/yarn/lib/*:/hq/install/hadoop-3.1.4/share/hadoop/yarn/*</value>
    </property>
    <property>
        <name>mapreduce.reduce.env</name>
        <value>HADOOP_MAPRED_HOME=/hq/install/hadoop-3.1.4/etc/hadoop:/hq/install/hadoop-3.1.4/share/hadoop/common/lib/*:/hq/install/hadoop-3.1.4/share/hadoop/common/*:/hq/install/hadoop-3.1.4/share/hadoop/hdfs:/hq/install/hadoop-3.1.4/share/hadoop/hdfs/lib/*:/hq/install/hadoop-3.1.4/share/hadoop/hdfs/*:/hq/install/hadoop-3.1.4/share/hadoop/mapreduce/lib/*:/hq/install/hadoop-3.1.4/share/hadoop/mapreduce/*:/hq/install/hadoop-3.1.4/share/hadoop/yarn:/hq/install/hadoop-3.1.4/share/hadoop/yarn/lib/*:/hq/install/hadoop-3.1.4/share/hadoop/yarn/*</value>
    </property>
</configuration>
```



#### 3.3.6.修改workers文件

第一台机器执行以下命令

```shell
vim workers
```

原内容替换为

```
node01
node02
node03
```

### 3.4. 第四步：创建文件存放目录

3.4.1. 第一台机器执行以下命令

node01机器上面创建以下目录

```shell
mkdir -p /kkb/install/hadoop-3.1.4/hadoopDatas/tempDatas
mkdir -p /kkb/install/hadoop-3.1.4/hadoopDatas/namenodeDatas
mkdir -p /kkb/install/hadoop-3.1.4/hadoopDatas/datanodeDatas 
mkdir -p /kkb/install/hadoop-3.1.4/hadoopDatas/dfs/nn/edits
mkdir -p /kkb/install/hadoop-3.1.4/hadoopDatas/dfs/snn/name
mkdir -p /kkb/install/hadoop-3.1.4/hadoopDatas/dfs/nn/snn/edits


##我的是
mkdir -p /hq/install/hadoop-3.1.4/hadoopDatas/tempDatas
mkdir -p /hq/install/hadoop-3.1.4/hadoopDatas/namenodeDatas
mkdir -p /hq/install/hadoop-3.1.4/hadoopDatas/datanodeDatas 
mkdir -p /hq/install/hadoop-3.1.4/hadoopDatas/dfs/nn/edits
mkdir -p /hq/install/hadoop-3.1.4/hadoopDatas/dfs/snn/name
mkdir -p /hq/install/hadoop-3.1.4/hadoopDatas/dfs/nn/snn/edits
```



### 3.5. 第五步：安装包的分发scp与rsync

在linux当中，用于向远程服务器拷贝文件或者文件夹可以使用scp或者rsync，这两个命令功能类似都是向远程服务器进行拷贝，只不过scp是全量拷贝，rsync可以做到增量拷贝，rsync的效率比scp更高一些

#### 3.5.1. 通过scp直接拷贝

scp（secure copy）安全拷贝

可以通过scp进行不同服务器之间的文件或者文件夹的复制

使用语法 

```shell
scp -r sourceFile  username@host:destpath
```

用法示例

```shell
scp -r hadoop-lzo-0.4.20.jar hadoop@node01:/kkb/
```

node01执行以下命令进行拷贝

```shell
cd /kkb/install/
scp -r hadoop-3.1.4/ node02:$PWD
scp -r hadoop-3.1.4/ node03:$PWD

###我的是
cd /hq/install/
scp -r hadoop-3.1.4/ node02:$PWD
scp -r hadoop-3.1.4/ node03:$PWD
```

 

#### 3.5.2. 通过rsync来实现增量拷贝

rsync 远程同步工具

rsync主要用于备份和镜像。具有速度快、<font color='red'>避免复制相同内容</font>和支持符号链接的优点。

rsync和scp区别：用rsync做文件的复制要比scp的速度快，rsync只对差异文件做更新。scp是把所有文件都复制过去。

<font color='red'>三台机器执行以下命令安装rsync工具</font>

```shell
sudo yum -y install rsync
```

（1）  基本语法

node01执行以下命令同步zk安装包

```shell
rsync -av /kkb/soft/apache-zookeeper-3.6.2-bin.tar.gz node02:/kkb/soft/

###我的是
rsync -av /hq/soft/apache-zookeeper-3.6.2-bin.tar.gz node02:/hq/soft/
```

命令 选项参数 要拷贝的文件路径/名称 目的用户@主机:目的路径/名称

选项参数说明

| **选项** | **功能**     |
| -------- | ------------ |
| -a       | 归档拷贝     |
| -v       | 显示复制过程 |

（2）案例实操

（3）把node01机器上的/kkb/soft目录同步到node02服务器的hadooop用户下的/kkb/目录

```shell
rsync -av /kkb/soft node02:/kkb/soft
```

 

#### 3.5.3. 通过rsync来封装分发脚本

我们可以通过rsync这个命令工具来实现脚本的分发，可以增量的将文件分发到我们所有其他的机器上面去

（1）需求：循环复制文件到所有节点的相同目录下

（2）需求分析：

（a）rsync命令原始拷贝：

```shell
rsync -av /kkb/soft hadoop@node02:/kkb/soft
```

（b）期望脚本使用方式：

xsync要同步的文件名称

（c）说明：在/home/hadoop/bin这个目录下存放的脚本，hadoop用户可以在系统任何地方直接执行。

（3）脚本实现

（a）在/home/hadoop目录下创建bin目录，并在bin目录下xsync创建文件，文件内容如下：

```shell
[hadoop@node01 ~]$ cd ~
[hadoop@node01 ~]$ mkdir bin
[hadoop@node01 bin]$ cd /home/hadoop/bin
[hadoop@node01 ~]$ touch xsync
[hadoop@node01 ~]$ vim xsync
```

在该文件中编写如下代码

```shell
#!/bin/bash
#1 获取输入参数个数，如果没有参数，直接退出
pcount=$#
if ((pcount==0)); then
echo no args;
exit;
fi

#2 获取文件名称
p1=$1
fname=`basename $p1`

echo $fname

#3 获取上级目录到绝对路径
pdir=`cd -P $(dirname $p1); pwd`
echo $pdir

#4 获取当前用户名称
user=`whoami`

#5 循环
for((host=1; host<4; host++)); do
       echo ------------------- node0$host --------------
       rsync -av $pdir/$fname $user@node0$host:$pdir
done
```

（b）修改脚本 xsync 具有执行权限

```shell
[hadoop@node01 bin]$ cd ~/bin/
[hadoop@node01 bin]$ chmod 777 xsync
```

（c）调用脚本形式：xsync 文件名称

```shell
[hadoop@node01 bin]$ xsync /home/hadoop/bin/
```

 

注意：如果将xsync放到/home/hadoop/bin目录下仍然不能实现全局使用，可以将xsync移动到/usr/local/bin目录下

### 3.6.第六步：配置hadoop的环境变量

三台机器都要进行配置hadoop的环境变量

三台机器执行以下命令

```shell
sudo vim /etc/profile
```

 

```shell
export HADOOP_HOME=/kkb/install/hadoop-3.1.4
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin

###我的是
export HADOOP_HOME=/hq/install/hadoop-3.1.4
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
```

配置完成之后生效

```shell
source /etc/profile
```

### 3.7.第七步：格式化集群

- 要启动 Hadoop 集群，需要启动 HDFS 和 YARN 两个集群。 

- 注意：首次启动HDFS时，必须对其进行格式化操作。本质上是一些清理和准备工作，因为此时的 HDFS 在物理上还是不存在的。<font color='red'>格式化操作只有在首次启动的时候需要，以后再也不需要了</font>

- <font color='red'>node01执行一遍即可</font>

```shell
hdfs namenode -format
```

- 或者

```shell
hadoop namenode –format
```

- 下图高亮表示格式化成功；

![image-20201019211525368](assets/image-20201019211525368.png)

### 3.8.第八步：集群启动

- 启动集群有两种方式：
  - ①脚本一键启动；
  - ②单个进程逐个启动

#### 3.8.1. 启动HDFS、YARN、Historyserver

- 如果配置了 etc/hadoop/workers 和 ssh 免密登录，则可以使用程序脚本启动所有Hadoop 两个集群的相关进程，在主节点所设定的机器上执行。

- 启动集群

- 主节点node01节点上执行以下命令

```shell
start-dfs.sh
start-yarn.sh
# 已过时mr-jobhistory-daemon.sh start historyserver
mapred --daemon start historyserver
```

- 停止集群（主节点node01节点上执行）：

```shell
stop-dfs.sh
stop-yarn.sh 
# 已过时 mr-jobhistory-daemon.sh stop historyserver
mapred --daemon stop historyserver
```

 

#### 3.8.2. 单个进程逐个启动

```shell
# 在主节点上使用以下命令启动 HDFS NameNode： 
# 已过时 hadoop-daemon.sh start namenode 
hdfs --daemon start namenode

# 在主节点上使用以下命令启动 HDFS SecondaryNamenode： 
# 已过时 hadoop-daemon.sh start secondarynamenode 
hdfs --daemon start secondarynamenode

# 在每个从节点上使用以下命令启动 HDFS DataNode： 
# 已过时 hadoop-daemon.sh start datanode
hdfs --daemon start datanode

# 在主节点上使用以下命令启动 YARN ResourceManager： 
# 已过时 yarn-daemon.sh start resourcemanager 
yarn --daemon start resourcemanager

# 在每个从节点上使用以下命令启动 YARN nodemanager： 
# 已过时 yarn-daemon.sh start nodemanager 
yarn --daemon start nodemanager


以上脚本位于$HADOOP_HOME/sbin/目录下。如果想要停止某个节点上某个角色，只需要把命令中的start 改为stop 即可。
```

#### 3.8.3. 一键启动hadoop集群的脚本

- 为了便于一键启动hadoop集群，我们可以编写shell脚本

- 在node01服务器的/home/hadoop/bin目录下创建脚本

```shell
[hadoop@node01 bin]$ cd /home/hadoop/bin/
[hadoop@node01 bin]$ vim hadoop.sh
```

- 内容如下

```shell
#!/bin/bash
case $1 in
"start" ){
 source /etc/profile;
 /hq/install/hadoop-3.1.4/sbin/start-dfs.sh
 /hq/install/hadoop-3.1.4/sbin/start-yarn.sh
 #/hq/install/hadoop-3.1.4/sbin/mr-jobhistory-daemon.sh start historyserver
 /hq/install/hadoop-3.1.4/bin/mapred --daemon start historyserver
};;
"stop"){

 /hq/install/hadoop-3.1.4/sbin/stop-dfs.sh
 /hq/install/hadoop-3.1.4/sbin/stop-yarn.sh
 #/hq/install/hadoop-3.1.4/sbin/mr-jobhistory-daemon.sh stop  historyserver
 /hq/install/hadoop-3.1.4/bin/mapred --daemon stop historyserver
};;
esac
```

- 修改脚本权限

```shell
[hadoop@node01 bin]$ chmod 777 hadoop.sh
[hadoop@node01 bin]$ ./hadoop.sh start  # 启动hadoop集群
[hadoop@node01 bin]$ ./hadoop.sh stop   # 停止hadoop集群
```



### 3.9.第九步：验证集群是否搭建成功

#### 3.9.1. 访问web ui界面

- hdfs集群访问地址

http://192.168.51.100:9870/

http://192.168.230.100:9870/

- yarn集群访问地址

http://192.168.51.100:8088

http://192.168.230.100:8088

- jobhistory访问地址：

http://192.168.51.100:19888

http://192.168.230.100:19888



- 若将linux的`/etc/hosts`文件的如下内容，添加到本机的hosts文件中(==ip地址根据自己的实际情况进行修改==)

```
192.168.51.100 node01.kaikeba.com  node01
192.168.51.110 node02.kaikeba.com  node02
192.168.51.120 node03.kaikeba.com  node03

###我的是
192.168.230.100 node01.huangqiang.com  node01
192.168.230.110 node02.huangqiang.com  node02
192.168.230.120 node03.huangqiang.com  node03
```

- windows的hosts文件路径是`C:\Windows\System32\drivers\etc\hosts`
- mac的hosts文件是`/etc/hosts`

- 那么，上边的web ui界面访问地址可以分别写程

  - hdfs集群访问地址

    http://node01:9870/

  - yarn集群访问地址

    http://node01:8088

  - jobhistory访问地址：

    http://node01:19888



#### 3.9.2. 所有机器查看进程脚本

- 我们也可以通过jps在每台机器上面查看进程名称，为了方便我们以后查看进程，我们可以通过脚本一键查看所有机器的进程

- 在node01服务器的/home/hadoop/bin目录下创建文件xcall

```shell
[hadoop@node01 bin]$ cd ~/bin/
[hadoop@node01 bin]$ vim xcall
```

 

- 添加以下内容

```shell
#!/bin/bash

params=$@
for (( i=1 ; i <= 3 ; i = $i + 1 )) ; do
    echo ============= node0$i $params =============
    ssh node0$i "source /etc/profile;$params"
done
```

- 然后一键查看进程并分发该脚本

```shell
chmod 777  /home/hadoop/bin/xcall
xsync /home/hadoop/bin/
```

- 各节点应该启动的hadoop进程如下图

```shell
xcall jps
```

![image-20200513122314381](assets/image-20200513122314381.png)

#### 3.9.3. 运行一个mr例子

- 任一节点运行pi例子

```
[hadoop@node01 ~]$ hadoop jar /kkb/install/hadoop-3.1.4/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.1.4.jar pi 5 5

###我的是
hadoop jar /hq/install/hadoop-3.1.4/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.1.4.jar pi 5 5
```

- 最后计算出pi的近似值

![image-20200522154422389](assets/image-20200522154422389.png)



==提醒：如果要关闭电脑时，清一定要按照以下顺序操作，否则集群可能会出问题==

- 关闭hadoop集群

- 关闭虚拟机

- 关闭电脑

