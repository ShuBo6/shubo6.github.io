---
title: Hadoop分布式集群搭建
date: 2019-03-06 13:23:56
tags: [Hadoop,Java,Linux]
---
<br>
#### Hadoop的搭建有三种方式:单机版适合开发调试；伪分布式版，适合模拟集群学习；完全分布式，生产使用的模式。本文记录搭建完全分布式环境的搭建。共使用了两台虚拟主机。

# 环境准备

### 使用两台centos7的虚拟主机。
### java1.8软件包
### hadoop2.7软件包

## 1.两台主机的ip和主机名分别为 

### `10.255.46.92 master`
### `10.255.46.93 slave`
## 2.安装jdk配置环境变量
### 我使用的是压缩包版本，解压后放到了`/usr/share/hadoop`下
### 在 `/etc/profile`文件中追加以下内容
``` conf
JAVA_HOME=/usr/share/java
JRE_HOME=$JAVA_HOME/jre
PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
export JAVA_HOME JRE_HOME PATH CLASSPATH
```
### 使用`source`命令生效环境变量
`source /etc/profile `
# 配置免密登录
## 1.关闭防火墙、关闭selinux
## 2.产生密钥
`ssh-keygen -t rsa`
## 3.将产生的密钥发送到指定的主机
``` bash
ssh-copy-id master
ssh-copy-id slave
```
## 4.测试免密登录是否生效
# hadoop环境配置
## 1.解压hadoop2.7 .tar.gz
``` bash
tar -zxvf hadoop2.7 .tar.gz -C（目录参数） 
```
## 2.修改hadoop文件目录下的`etc/hadoop/hadoop-env.sh`脚本文件二十五行处的`JAVA_HOME`，指定为绝对路径。
## 3.修改Hadoop核心配置文件`etc/hadoop/core-site.xml`，通过`fs.default.name`指定NameNode的IP地址和端口号，通过`hadoop.tmp.dir`指定hadoop数据存储的临时文件夹。
``` conf
<configuration>
       <property>
                <name>fs.defaultFS</name>
                <value>hdfs://master:8020</value>
       </property>
       <property>
               <name>hadoop.tmp.dir</name>
               <value>file:/usr/share/hadoop/tmp</value>
               <description>Abase for other temporary   directories.</description>
       </property>
</configuration>
```
## 4.修改HDFS核心配置文件`etc/hadoop/hdfs-site.xml`，通过`dfs.replication`指定HDFS的备份因子为3，通过`dfs.name.dir`指定namenode节点的文件存储目录，通过`dfs.data.dir`指定datanode节点的文件存储目录。
``` conf
<configuration>
     <property>
             <name>dfs.namenode.name.dir</name>
             <value>file:/usr/share/hadoop/dfs/name</value>
       </property>
      <property>
              <name>dfs.datanode.data.dir</name>
              <value>file:/usr/share/hadoop/dfs/data</value>
       </property>
       <property>
               <name>dfs.replication</name>
               <value>3</value>
        </property>
</configuration>
```
## 5.配置`mapred-site.xml`文件拷贝`mapred-site.xml.template`为`mapred-site.xml`，在进行修改
``` conf
<configuration>
  <property>
      <name>mapreduce.framework.name</name>
      <value>yarn</value>
  </property>
</configuration>
```
## 6.配置yarn-site.xml
``` conf
<configuration>
<!-- Site specific YARN configuration properties -->
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
    <property>
        <name>yarn.resourcemanager.hostname</name>
        <value>master</value>
    </property>
</configuration>
```
## 7.配置slaves文件（Master主机特有）修改`etc/hadoop/slaves`文件。
{% asset_img p2.png p2 %}
# 拷贝文件到slave
## 1.拷贝之前先格式化并检查错误
`bin/hadoop namenode -format`
## 2.拷贝hadoop到其他节点（每个节点都需要拷贝，java环境也是同理）
`scp -r /usr/share/hadoop slave:/usr/share/`
## 3.启动 `sbin/start-all.sh`
## 4.使用jps命令查看运行情况
{% asset_img p1.png p1 %}






---
##### 开学第三天