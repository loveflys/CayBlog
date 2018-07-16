---
title: HBase Filter使用方法（三）------批量put导入
date: 2015-01-31 17:26:40
tags: HBase
---

一、应用线程导入

1、创建put方法
```
public class mmm{
    public static Configuration config=new Configuration();;
    static{
        config.set("hbase.zookeeper.property.clientPoint","2181");
        config.set("hbase.zookeeper.quorum","hbase");
        config.set("dfs.socket.timeout", "180000");
    }
    public static void put(String tablename,String RowKey,String Family,String Qualifier,String Value){
		HTable h=null;
		try{
			h=new HTable(config,tablename);
			Put put=new Put(Bytes.toBytes(RowKey));
			put.add(Bytes.toBytes(Family), Bytes.toBytes(Qualifier), Bytes.toBytes(Value));
			h.put(put);
		}catch(Exception e){
			e.printStackTrace();
		}finally{
			try {
				h.close();
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
	}
}
```

2、创建线程

```
public class PutXX {
    public static void main(String[] args) {
        run1 r1=new run1();
        Thread rr1=new Thread(r1);
        rr1.start();
        run2 r2=new run2();
        Thread rr2=new Thread(r2);
        rr2.start();
        //这里创建两个线程，需要可以继续创建~
	}
}
class run1 implements Runnable{
	public void run() {
		for(int i=0;i<=10000000;i++){
			mmm.put("yuan", ""+i+"", "property", "yuan_name", "xx"+i);
			System.out.println(i);
		}
	}
}
class run2 implements Runnable{
	public void run() {
		for(int i=10000001;i<=20000000;i++){
			mmm.put("yuan", ""+i+"", "property", "yuan_name", "xx"+i);
			System.out.println(i);
		}
	}
}
```

 <2015.2.2>

数据导入速度慢。之前put方法每执行一次就要new一个新HTable然后释放资源。。太墨迹了

新put


```
public static void NBput(String tablename,int RowKey,String Family,String Qualifier,String Value){
	HTable h=null;
	try {
		h=new HTable(config,tablename);
		for(int i=RowKey;i<=(RowKey+10000000);i++){
			String row=""+i+"";
			Put put=new Put(Bytes.toBytes(row));
			put.add(Bytes.toBytes(Family), Bytes.toBytes(Qualifier), Bytes.toBytes(Value));
			h.put(put);
			System.out.println(i);
		}
	} catch (IOException e) {
		e.printStackTrace();
	}finally{
		try {
			h.close();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
}
```

修改以后速度明显提升啊我去！之前那方法太傻缺了。。。。


二、使用List进行put

```
public class mmm{
    public static Configuration config=new Configuration();
    static{
        config.set("hbase.zookeeper.property.clientPoint","2181");
        config.set("hbase.zookeeper.quorum","hbase");
        config.set("dfs.socket.timeout", "180000");
    }
    /*
     * 批量put
     * 
     */
    public static void moreput(String tablename, List<Put> puts){
        HTable h=null;
        try{
            h=new HTable(config,tablename);
            Put put=new Put(Bytes.toBytes(""));
            puts.add(put);
            h.put(puts);
        }catch(Exception e){
            e.printStackTrace();
        }finally{
            try {
                puts.clear();
                h.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

然后在主函数中：
```
	List<Put> puts=new ArrayList<Put>();
    for(int i=100000;i<=5000000;i++){
        System.out.println(i);
        Put put=new Put(Bytes.toBytes(""+i+""));
        put.add(Bytes.toBytes("property"), Bytes.toBytes("yuan_name"), Bytes.toBytes("源网"+i));
        puts.add(put);
	}
    System.out.println("写入List完成");
    mmm.moreput("yuan", puts);
```