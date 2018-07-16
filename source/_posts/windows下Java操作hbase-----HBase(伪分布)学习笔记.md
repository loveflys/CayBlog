---
title: windows下Java操作hbase-----HBase(伪分布)学习笔记
date: 2015-01-25 13:30:16
tags: HBase
---

一、修改hosts文件

进入C:\Windows\System32\drivers\etc目录

编辑hosts文件，在底部添加  10.10.10.10（要跟linux机器中hosts里的ip一样） hbase

保存退出

二、安装maven（方便管理jar包，也可以不安装）

具体安装步骤参考http://blog.csdn.net/chenxuejiakaren/article/details/7938524

在eclipse中安装请参考http://blog.csdn.net/wode_dream/article/details/38052639

三、创建项目

新建->项目->选择maven文件夹下的maven project->类型选择maven-archetype-quickstart->

group id 是和artifact id 组合起来是完整的 package,一般artifact id 是项目名->建好之后,双击pom.xml->切换到最后一个卡pom.xml->

在 dependencies 里追加这么一截 
```
<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-client</artifactId>
    <version>0.98.9-hadoop2</version>
</dependency> 
```
    保存就OK了！

如果报错：
```
    Missing artifact jdk.tools:jdk.tools
```
再追加这一截
```
<dependency>

    <groupId>jdk.tools</groupId>

    <artifactId>jdk.tools</artifactId>

    <version>1.8</version>

    <scope>system</scope>

    <systemPath>${JAVA_HOME}/lib/tools.jar</systemPath>

</dependency>
```
    

四、操作hbase

在包下面自己新建class

    

注意，调用的时候要直接

DBhelper.方法();

```
package 包名;
import java.io.IOException;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HColumnDescriptor;
import org.apache.hadoop.hbase.HTableDescriptor;
import org.apache.hadoop.hbase.KeyValue;
import org.apache.hadoop.hbase.MasterNotRunningException;
import org.apache.hadoop.hbase.ZooKeeperConnectionException;
import org.apache.hadoop.hbase.client.Get;
import org.apache.hadoop.hbase.client.HBaseAdmin;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.client.Result;
import org.apache.hadoop.hbase.client.ResultScanner;
import org.apache.hadoop.hbase.client.Scan;
import org.apache.hadoop.hbase.util.Bytes;
public class DBhelper {
 public static Configuration config = new Configuration();
 static{
config.set("hbase.zookeeper.property.clientPoint", "2181");
config.set("hbase.zookeeper.quorum", "hbase");
 }
 /*
  * 按条件查询value
  * 参数：表名，行，列簇，列名
  */
 public static void get(String tablename,String rowKey,String Family,String qualifier)throws Exception{
 HTable h=new HTable(config,tablename);
 System.out.println("开始获取------哔哔哔");
 Get get=new Get(rowKey.getBytes());
 get.addColumn(Family.getBytes(), qualifier.getBytes());
 Result r=h.get(get);
for(KeyValue kv:r.raw())
{
System.out.println("您要查的值为"+new String(kv.getValue()));
}
 }
 /*
  * 扫描table
  * 参数：表名
  */
 public static void scan(String tablename)throws Exception{
    HTable h=new HTable(config,tablename);
    System.out.println("开始扫描");
    ResultScanner rs=h.getScanner(new Scan(tablename.getBytes()));
    for(Result r:rs){
        for(KeyValue kv:r.raw()){
            System.out.println(new String(kv.getRow())+"   "+new String(kv.getFamily())+"   "+new String(kv.getQualifier())+"   "+new String(kv.getValue())+"   "+kv.getTimestamp());
        }
    }
    System.out.println("扫描结束");
 }
 /*
  * 添加或者修改数据
  * 参数：表名，行，列簇，列名，值
  */
 public static void put(String tablename,String rowKey,String Family,String qualifier,String value)throws Exception{
    HTable h=new HTable(config,tablename);
    System.out.println("- - - - - -  - -  - -  - 正在添加- - - - - - - - - - - - ");
    Put put =new Put(Bytes.toBytes(rowKey));
    put.add(Bytes.toBytes(Family), Bytes.toBytes(qualifier), Bytes.toBytes(value));
    h.put(put);
    System.out.println("- - - - - -  - -  - -  - 添加完成- - - - - - - - - - - - ");
 }
  
  
 /*
  * 删除table
  * 参数：表名
  */
 public static void Delete(String tablename) throws Exception{
    HBaseAdmin h=new HBaseAdmin(config);
    if(h.tableExists(tablename)){
        System.out.println("正在删除"+tablename);
        h.disableTable(tablename);
        h.deleteTable(tablename);
        System.out.println("删除成功");
    }else{
        System.out.println("傻啊！"+tablename+"不存在");
    }
 }
  
  
 /*
  * 创建table
  * 参数：表名+列簇
  */
 public static void Create(String tablename,String Col)throws Exception{
    HBaseAdmin admin=new HBaseAdmin(config);
    if(admin.tableExists(tablename)){
        System.out.println(tablename+"已存在了！");
    }else{
        System.out.println("正在创建table："+tablename);
        HTableDescriptor htd=new HTableDescriptor(tablename);
        System.out.println("创建成功！正在添加列簇："+Col);
        htd.addFamily(new HColumnDescriptor(Col));
        admin.createTable(htd);
        System.out.println("创建完成：/t 表名："+tablename+"\t 列簇："+Col);
    }
 }
}
```