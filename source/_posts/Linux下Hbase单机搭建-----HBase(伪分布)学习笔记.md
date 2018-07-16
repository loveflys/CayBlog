---
title: Linux下HBase单机搭建-----HBase(伪分布)学习笔记
date: 2015-01-25 13:08:47
tags: HBase
---

一、Linux搭建Hbase

1、环境

系统：CentOs6.6     下载地址：http://mirrors.neusoft.edu.cn/centos/6.6/isos/x86_64/

Hbase：Hbase-0.98.9 下载地址http://mirrors.hust.edu.cn/apache/hbase/hbase-0.98.9/

jdk：jdk1.8.0_31    下载地址http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html  下载linux x64.tar.gz

2、安装

1)、下载hbase选择hbase-0.98.9-hadoop2-bin.tar.gz

2)、进入下载目录下,tar zxvf hbase-0.98.9-hadoop2-bin.tar.gz /home/baozi/hbase  (/home/baozi/hbase是你要安装到的目录，自行设置)

3)、tar zxvf jdk-8u31-linux-x64.tar.gz /home/baozi/jdk8  (/home/baozi/jdk是你要安装到的目录，自行设置)

4)、进入安装目录 

cd ~

cd /home/baozi

修改文件夹名称mv hbase-0.98.9-hadoop2 hbase

mv jdk解压目录 jdk8

5)、修改hosts
```
ifconfig   查看ip地址

vi /etc/hosts  添加ip地址 host   eg：10.10.10.10  hbase
```
6)、配置环境变量   
```
vi ~/.bash_profile

export HBASE_HOME=/home/baozi/hbase

export JAVA_HOME=/home/baozi/jdk8 

export PATH=${PATH}:${HBASE_HOME}/bin
```
注意！！！！！  修改完以后重新加载一下
```
source ~/.bash_profile    
```
7)、进入hbase目录下的conf文件夹    
```
    cd /home/baozi/hbase/conf

    vi hbase-site.xml

    在configuration中添加

    <property>

        <name>hbase.rootdir</name>

        <value>/home/baozi/hbase</value>

    </property>

    <property>

        <name>hbase.zookeeper.quorum</name>

        <value>hbase</value>

    </property>
```
保存后vi hbase-env.sh

设置export JAVA_HOME=/home/baozi/jdk8

保存退出

8)、此时    start-hbase.sh 即可开启hbase

hbase shell进入hbase命令行

大！！功！！告！！成！！