---
title: Hive安装与配置
date: 2019-03-23 16:28:21
tags: [Hadoop,Linux]
---

<br>

## Hive
Hive是基于Hadoop构建的一套数据仓库分析系统，它提供了丰富的SQL查询方式来分析存储在Hadoop 分布式文件系统中的数据。其在Hadoop的架构体系中承担了一个SQL解析的过程，它提供了对外的入口来获取用户的指令然后对指令进行分析，解析出一个MapReduce程序组成可执行计划，并按照该计划生成对应的MapReduce任务提交给Hadoop集群处理，获取最终的结果。元数据——如表模式——存储在名为metastore的数据库中。
## Metastore
metastore是Hive元数据集中存放地。它包括两部分：服务和后台数据存储。有三种方式配置metastore：内嵌metastore、本地metastore以及远程metastore。
本次搭建中采用MySQL作为远程仓库，部署在master节点上，hive服务端也安装在master上，hive客户端即hadoop-slave访问hive服务器。
# 下载
### hive：可以选择国内apache镜像站下载hive
### mysql数据库java驱动：[mysql官网下载](https://dev.mysql.com/downloads/connector/j/)
# 配置
### 1.安装mysql/mariadb数据库，启动并建立hive数据库。
### 2.开启远程连接权限
``` bash
> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%'IDENTIFIED BY '000000' WITH GRANT OPTION;
> flush privileges;
```
### 3.复制hive目录下的`conf/hive-default.xml.template`改名为`conf/hive-site.xml`
### 4.编辑`conf/hive-site.xml`文件
``` conf
<configuration>
        <property>
                <name>hive.metastore.local</name>
                <value>true</value>
        </property>
        <property>
                <name>javax.jdo.option.ConnectionURL</name>
                <value>jdbc:mysql://master:3306/hive?characterEncoding=UTF-8</value>
        </property>
        <property>
                <name>javax.jdo.option.ConnectionDriverName</name>
                <value>com.mysql.jdbc.Driver</value>
        </property>
        <property>
                <name>javax.jdo.option.ConnectionUserName</name>
                <value>hive</value>
        </property>
        <property>
                <name>javax.jdo.option.ConnectionPassword</name>
                <value>000000</value>
        </property>
        <property>
                <name>hive.server2.authentication</name>
                <value>NONE</value>
        </property>
        <property>
                <name>hive.server2.thrift.client.user</name>
                <value>root</value>
        </property>
        <property>
                <name>hive.server2.thrift.client.password</name>
                <value>000000</value>
        </property>
</configuration>
```
### 5.配置环境变量
``` conf
JAVA_HOME=/usr/share/jdk
JRE_HOME=$JAVA_HOME/jre
HADOOP_HOME=/usr/share/hadoop
HIVE_HOME=$HADOOP_HOME/apache-hive-2.3.4-bin
PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin:$HADOOP_HOME/bin:$HIVE_HOME/bin
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
export JAVA_HOME JRE_HOME PATH CLASSPATH HADOOP_HOME HIVE_HOME

```
### 6.格式化数据库（如果漏掉这一步，hive启动会报错）
``` bash
./schematool --dbType mysql --initSchema
```
### 7.启动Hive CLI
``` bash
$ hive
```
### 8.将hive传输到slave节点
``` bash
scp -r /usr/share/hadoop/apache-hive-2.3.4-bin/ slave:/usr/share/hadoop
```
### 9.修改slave节点的`conf/hive-site.xml`文件
``` conf
<configuration>
        <property>
                <name>hive.metastore.uris</name>
                <value>thrift://master:9083</value>
        </property>
</configuration>            
```
