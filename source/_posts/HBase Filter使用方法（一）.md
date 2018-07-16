---
title: HBase Filter使用方法（一）
date: 2015-01-29 16:24:19
tags: HBase
---

一、FilterList
```
// 通过将operator参数设置为Operator.MUST_PASS_ONE,达到list中各filter为"或"的关系   or
// 默认operator参数的值为Operator.MUST_PASS_ALL,即list中各filter为"并"的关系       and
FilterList f=new FilterList(FilterList.Operator.MUST_PASS_ONE);
```

二、几大Filter

1、Comparision Filters

1.1 RowFilter    			√

1.2 FamilyFilter			√

1.3 QualifierFilter     		√

1.4 ValueFilter				√

1.5 DependentColumnFilter		×

2、Dedicated Filters

2.1 SingleColumnValueFilter		×

2.2 SingleColumnValueExcludeFilter	×

2.3 PrefixFilter			×

2.4 PageFilter				×

2.5 KeyOnlyFilter			×

2.6 FirstKeyOnlyFilter			×

2.7 TimestampsFilter			×

2.8 RandomRowFilter			×

3、Decorating Filters

3.1 SkipFilter				×

3.2 WhileMatchFilters			×

---

1.1   RowFilter  

格式：RowFilter(CompareFilter.CompareOp rowCompareOp, ByteArrayComparable rowComparator)



参数一  Operator	Description

LESS	小于

LESS_OR_EQUAL	小于等于

EQUAL	等于

NOT_EQUAL	不等于

GREATER_OR_EQUAL	大于等于

GREATER	大于

NO_OP	排除所有



参数二  ByteArrayComparable  可用

BinaryComparator, BinaryPrefixComparator, BitComparator, LongComparator, NullComparator, RegexStringComparator, SubstringComparator



例子：Filter filter=new RowFilter(CompareOp.EQUAL, new BinaryComparator(Bytes.toBytes(RowKey))



相关的过滤方法使用:

提取rowkey以01结尾数据--正则表达式

Filter filter = new RowFilter(CompareFilter.CompareOp.EQUAL,new RegexStringComparator(".*01$"));



提取rowkey以包含201407的数据

Filter filter = new RowFilter(CompareFilter.CompareOp.EQUAL,new SubstringComparator("201407"));





提取rowkey以123开头的数据

Filter filter = new RowFilter(CompareFilter.CompareOp.EQUAL,new BinaryPrefixComparator("123".getBytes()));





提取rowkey小于等于row010的行

Filter filter1 = new RowFilter(CompareFilter.CompareOp.LESS_OR_EQUAL,new BinaryComparator("row010".getBytes()));

1.2   FamilyFilter（同上）

1.3   QualifierFilter（同上）--------当列为数字，不管用了？why--------

1.4   ValueFilter（同上）

1.5   DependentColumnFilter    未完待续！

