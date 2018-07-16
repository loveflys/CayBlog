---
title: HBase Filter使用方法（二）
date: 2015-01-31 17:15:57
tags: HBase
---

2、Dedicated Filters

2.1 SingleColumnValueFilter		√

2.2 SingleColumnValueExcludeFilter	√

2.3 PrefixFilter			√

2.4 PageFilter				√

2.5 KeyOnlyFilter			√

2.6 FirstKeyOnlyFilter			√

2.7 TimestampsFilter			×

2.8 RandomRowFilter			√

---

2.1   SingleColumnValueFilter

例子：Filter filter=new SingleColumnValueExcludeFilter(Bytes.toBytes(Family), Bytes.toBytes(Qualifier), CompareOp.EQUAL, Bytes.toBytes(Value));

2.2   SingleColumnValueExcludeFilter

使用：跟singlecolumnvaluefilter正好相反，这个是显示表中除了过滤的这条以外的所有数据 

例子：
```
Filter filter=new SingleColumnValueExcludeFilter(Bytes.toBytes(Family), Bytes.toBytes(Qualifier), CompareOp.EQUAL, Bytes.toBytes(Value));
((SingleColumnValueExcludeFilter) filter).setFilterIfMissing(true);
```
注意：！需要加((SingleColumnValueExcludeFilter) filter).setFilterIfMissing(true); 

2.3   PrefixFilter   和ColumnPrefixFilter    

使用：根据Row或Column的前缀取数据

例子：Filter filter=new PrefixFilter(Bytes.toBytes("r"));

取出RowKey以r开头的所有数据

2.4    PageFilter

通过设置pageside返回每一页page的数量

```
	final byte[] POSTFIX = new byte[] { 0x00 };  
	HTable table;
	try {
		table = new HTable(config, tablename);
		Filter filter = new PageFilter(pageside);  
		byte[] lastRow = null;  
		int totalRows = 0;  
		while (true) {  
			Scan scan = new Scan();  
			scan.setFilter(filter);  
			if(lastRow != null){  
				//注意这里添加了POSTFIX操作，不然死循环了  
				byte[] startRow = Bytes.add(lastRow,POSTFIX);  
				scan.setStartRow(startRow);  
			}  
			ResultScanner scanner = table.getScanner(scan);  
			int localRows = 0;  
			Result result;  
			while((result = scanner.next()) != null){  
				System.out.println(localRows++ + ":" + result);  
				totalRows ++;  
				lastRow = result.getRow();  
			}  
			scanner.close();  
			if(localRows == 0) break;  
		}  
		System.out.println("total rows:" + totalRows);  
	} catch (IOException e) {
		// TODO Auto-generated catch block
		e.printStackTrace();
	}
```


2.5    KeyOnlyFilter 

* 一般与其他过滤器配合使用

* Filter：KeyOnlyFilter(boolean lenAsVal)

* lenAsVal默认为假，表示不把val的长度作为val。否则val的长度将作为val输出。

* 键过滤器可以简单的设置过滤的结果集中只包含键而忽略值，这里有一个选项可以把结果集的值保存为值的长度

例子：
```
Filter filter = new KeyOnlyFilter(false);  
```
2.6    firstkeyonlyFilter

用法：同上，但仅会返回相同key的第一条kv

2.8    RandomRowFilter  

随即的返回row的数据，构造函数为
```
RandomRowFilter(float chance)  
```
chance取值为0到1.0，如果<0则为空，如果>1则包含所有的行。

例子：
```
Filter filter=new RandomRowFilter(0.5f)
```