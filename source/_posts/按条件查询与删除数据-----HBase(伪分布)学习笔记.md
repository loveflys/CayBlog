---
title: 按条件查询与删除数据-----HBase(伪分布)学习笔记
date: 2015-01-26 20:20:21
tags: HBase
---

```
 /*
  * 删除RowKey所有行
  * 非空判断√
  */
   
   
 public static void deleteRows(String tablename,String RowKey){
    HTable h=null;
    HBaseAdmin ha=null;
    Get get=new Get(Bytes.toBytes(RowKey));
    try{
        ha=new HBaseAdmin(config);
        if(ha.tableExists(tablename)){
            h=new HTable(config,tablename);
            Delete d=new Delete(Bytes.toBytes(RowKey));
            if(h.exists(get)){
                h.delete(d);
                System.out.println("删除成功");
            } else {
                System.out.println("滚犊子!!!");
            }
        } else {
            System.out.println("表呢？你的表那！！");
        }
    } catch(Exception e) {
        e.printStackTrace();
    } finally {
        try {
            h.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
  
  
 /*
  * 删除指定一行的数据
  *
  */
   
   
   
 public static void deleteRow(String tablename,String RowKey,String family,String qualifier){
    HTable h=null;
    try {
        h=new HTable(config,tablename);
        Delete d=new Delete(Bytes.toBytes(RowKey));
        d.deleteColumn(Bytes.toBytes(family), Bytes.toBytes(qualifier));
        h.delete(d);
        System.out.println("删除成功!");  
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
          
          
 /*
  * 按条件查询（应用filter）
  */
   
   
 @SuppressWarnings("deprecation")
public static void GetByParam(String tablename,String Family,String qualifier,String value){
    HTable h=null;
    ResultScanner rs=null;
    try {
        h=new HTable(config,tablename);
        Filter f=new SingleColumnValueFilter(Bytes.toBytes(Family),Bytes.toBytes(qualifier),CompareOp.EQUAL,Bytes.toBytes(value) );
        Scan scan=new Scan();
        scan.setFilter(f);
        scan.addColumn(Bytes.toBytes(Family),Bytes.toBytes(qualifier));
        rs=h.getScanner(scan);
        System.out.println("行     列簇     列名     值             时间戳");
        for (Result r : rs) {  
            for (KeyValue kv : r.raw()) {  
                System.out.println(kv.getRowOffset()+"    "+Family+"       "+qualifier+"    "+new String(kv.getValue())+"    "+ kv.getTimestamp());
            }  
        }  
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        try{
            rs.close();  
            h.close();
        }catch(Exception e){
            e.printStackTrace();
        }
    }
}
              
              
 /*
  * 按条件查询value
  * 参数：表名，行，列簇，列名
  */
   
   
 public static void get(String tablename,String rowKey,String Family,String qualifier)throws Exception{
    HConnection connection = HConnectionManager.createConnection(config);
    HTableInterface table = connection.getTable(TableName.valueOf(tablename));
    try {
        System.out.println("开始获取------哔哔哔");
        Get get=new Get(rowKey.getBytes());
        get.addColumn(Family.getBytes(), qualifier.getBytes());
        Result r=table.get(get);
        for(KeyValue kv:r.raw()){
            System.out.println("您要查的值为"+new String(kv.getValue()));
        }
    } finally {
        table.close();
        connection.close();
    } 
}
```