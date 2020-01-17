---
layout: post
title: 使用load导出mysql表数据
date: 2014-10-28
categories:
- 技术
description: 当我们需要备份数据库时，需要导出数据，我们一般是使用mysqldump命令来进行数据的导出，下面我们先来试验一下...
---

## 1. 准备

当我们需要备份数据库时，需要导出数据，我们一般是使用mysqldump命令来进行数据的导出，下面我们先来试验一下

首先创建一张表
```sql
CREATE TABLE `t1` (
	`id` int(11) DEFAULT NULL,
	`name` varchar(32) NOT NULL DEFAULT ''
) ENGINE=MyISAM DEFAULT CHARSET=utf8;
```
创建t1表，只有一个字段，往数据表中插入几行数据
```sql
INSERT INTO `t1` VALUES (1,'tom'),(2,'jack'),(3,'rose');  
```

## 2. mysqldump导出

然后使用mysqldump开始备份

```
>mysqldump -uroot -proot test t1 -F -l > /tmp/test.sql
```

这样我们就备份完毕了，我们可以查看一下test.sql的部分内容
```sql
--
-- Table structure for table `t1`
--

DROP TABLE IF EXISTS `t1`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `t1` (
	`id` int(11) DEFAULT NULL,
	`name` varchar(32) NOT NULL DEFAULT ''
) ENGINE=MyISAM DEFAULT CHARSET=utf8;
/*!40101 SET character_set_client = @saved_cs_client */;
```

可以看到里面有一大堆表结构的语句和表创建的语句，当数据量非常大的时候，导出的速度就可想而知。

于是我们就需要在导出的时候避开这些语句，这里就要用到load的infile和outfile来导入导出数据。

## 3. outfile导出

导出outfile的使用
```sql
	SELECT * FROM `t1` INTO OUTFILE "/tmp/t1.outfile.sql"
```
可以通过select查询将要导出的数据表和数据表的字段导出来，可以导出全部列和指定列，如：
```sql
	SELECT `id` FROM `t1` INTO OUTFILE "/tmp/t1.outfile.sql"
```

这样就导出了t1表的id列。

看一下导出的文件格式

![sql文件格式][1]

可以看到只导出了数据，而不存在表的结构和其他不相干的语句，这样就大大减少了导出数据的量，当大数据量时，速度就会明显提升了

导入load data的使用

```
	LOAD DATA INFILE "/tmp/t1.outfile.sql" INTO TABLE `t1`
```

这样是导入到t1表的全部列中，也可以导入到某一列或几列中
```
	LOAD DATA INFILE "/tmp/t1.outfile.sql" INTO TABLE `t1`(`id`)
```


-------

## 3. mysqldump和load data比较

下面进行一下实际操作，在t1表中插入1000万行数据进行导入导出速度的比较。
```
	mysql> select count(*) from t1;        
	+----------+
	| count(*) |
	+----------+
	| 10485760 |
	+----------+
```

### 3.1. 用outfile导出数据
```
	mysql> select * from t1 into outfile "/tmp/outfile.sql";
	Query OK, 10485760 rows affected (2.43 sec)
```

发现导出的速度非常快，1000万行数据只需要2秒多



### 3.2. 使用mysqldump导出数据

![导出时间][2]

发现导出需要12秒左右，是outfile的5倍左右

### 3.3. 比较两个文件的大小

![文件大小][3]

可以看到mysqldump导出的数据是outfile的2倍，这也就说明了outfile的优势所在。

-------

### 3.4. 使用load data infile导入
```
	mysql> load data infile "/tmp/outfile.sql" into table t1;    
	Query OK, 10485760 rows affected (12.46 sec)
	Records: 10485760  Deleted: 0  Skipped: 0  Warnings: 0


	mysql> select count(*) from t1;                      
	+----------+
	| count(*) |
	+----------+
	| 10485760 |
	+----------+
```

发现导入的速度也是不慢的

## 4. 远程导入数据
假设客户端与mysql服务器不在同一机器，导入文件在客户端机器上 d:/outfile.sql ，在客户机登录mysql后，执行下面导入语句：
```
	load data local infile  "d:/outfile.sql" into table t1;
```

正常情况是可以成功的，如果出现提示：
>ERROR 1148 (42000): The used command is not allowed with this MySQL version

出现这种情况一般是由于MySQL限制了客户端导入本地文件的权限，可以查看一下

```
	mysql> show variables like 'local%';
	+---------------+-------+
	| Variable_name | Value |
	+---------------+-------+
	| local_infile  | OFF   |
	+---------------+-------+
```

在这种情况下，可以有两种方法解决

1.连接mysql远程客户端，添加加--local-infile=1参数。
```
>mysql -u xxx -p xxx database -h xxx --local-infile=1
```

2.修改全局属性
```

	mysql> set global local_infile = 1;
	Query OK, 0 rows affected (0.00 sec)
	mysql> show variables like 'local%';
	+---------------+-------+
	| Variable_name | Value |
	+---------------+-------+
	| local_infile  | ON    |
	+---------------+-------+
	1 row in set (0.00 sec)
```


[1]: /images/20140904110643.jpg  "sql文件格式"
[2]: /images/20131130142325812.png "导出时间"
[3]: /images/20131130142531125.png "文件大小"