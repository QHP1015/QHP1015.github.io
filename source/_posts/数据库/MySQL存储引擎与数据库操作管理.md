---
title: MySQL存储引擎与数据库操作管理
author: 从此在回忆里
date: 2021-10-04 16:39:19
tags: 大学课程
categories: 数据库
---

### 存储引擎

存储引擎即如何存储数据、如何为存储的数据建立索引和如何更新、查询数据的机制

在关系数据库中数据的存储以表的形式存储，所以存储引擎也可以称为表类型

<!--more-->

MySQL提供插件式的存储引擎

```sql
--查看当前MySQL数据库支持的存储引擎
SHOW ENGINES ;
SHOW ENGINES \G
--查看当前数据库存储引擎
SHOW VARIABLES LIKE '%storage_engine%'
```

#### InnoDB存储引擎（OLTP）

InnoDB是事务型数据库的首选引擎，支持事务安全表（ACID），支持行锁定和外键

- InnoDB给MySQL提供具有提交、回滚和崩溃恢复能力的事务安全（ACID兼容）存储引擎
- InnoDB是为处理巨大数据量提供最大性能而设计的
- InnoDB存储引擎完全与MySQL服务器整合，InnoDB存储引擎为在主内存中缓存数据和索引而维持它自己的缓冲池
- InnoDB支持外键完整性约束
- InnoDB被用在众多需要高性能的大型数据库站点上

#### MyISAM存储引擎（OLAP)

MyISAM拥有较高的插入、查询速度，但不支持事务

- 大文件（达63位文件长度）
- 当把删除和更新及插入操作混合使用时，动态尺寸的行产生更少碎片
- 每个MyISAM表最大索引数时64，可以通过重新编译来改变
- BLOB和TEXT列可以被索引
- NULL值被允许在索引的列中，占每个键的0或1个字节
- 所有数字键值以高字节优先为原则被存储，以允许一个更高的索引压缩
- MyISAM类型表的`AUTO_INCREMENT`列更新比InnoDB类型的`AUTO_INCREMENT`更快
- 可以把数据文件和索引文件存储在不同的目录
- 每个字符列可以有不同的字符集
- `VARGHAR`的表可以固定或动态地记录长度

使用该引擎会生成三个文件 ：  .FRM  .MYD  .MYI

#### MEMORY存储引擎

适合用于存储临时数据的临时表，以及数据仓库中的维度表（默认使用哈希索引）

- 每个表可以有多达32个索引，每个索引16列，以及500字节的最大键长度
- 执行HASH和BTREE索引
- 在一个MEMORY表中可以有非唯一键
- 表使用一个固定的记录长度格式
- 不支持`BLOB`和`TEXT`列
- 支持`AUTO_INCREMENT`列和对可包含`NULL`值的列的索引

#### MERGE存储引擎

是一组MyISAM表的组合，这些表必须结构完全相同，MERGE表本身没有数据。查询、更新、删除实际是对内部的MyISAM表进行

对MERGE表进行`DROP`操作，只是删除MERGE定义，对内部的表没有影响

#### BLACKHOLE存储引擎

不能存储数据，会记录Binlog（二进制日志）

- 验证存储文件语法的正确性
- 来自二进制日志记录的开销测量，通过比较，允许与禁止二进制日志功能的BLACKHOLE的性能
- 用来查找与存储和引擎自身不相关的性能瓶颈

#### CSV存储引擎

该引擎实际上操作的就是一个标准的CSV文件，不支持索引

#### ARCHIVE存储引擎

主要用于通过较小的存储空间来存储过期的很少访问的历史数据

不支持索引

不支持删除，修改操作，仅支持插入和查询操作    行级锁定

#### 选择

MyISAM：适用于不需要事务支持、并发相对较低、数据修改相对较少、以读为主、数据一致性要求不是非常高

InnoDB：适用于需要事务支持，行级锁定对高并发有很好的适应能力，但需要确保查询时通过索引完成的，数据更新较为频繁

MEMORY：适用于需要很快的读写速度、对数据的安全性要求较低的场景

### 字符集

```sql
SHOW CHARACTER SET       --查看所有可以使用的字符集
information_schema. CHARACTER_SETS   --显示所有的字符集和对应的校对规则

SHOW COLLATION LIKE '***'    --查看相关字符集的校对规则
```

MySQL字符集包括**字符集**和**校对规则**

字符集和校对规则是一对多的关系，两个不同的字符集不能有相同的校对规则

每个字符集有一个默认校对规则，每个字符集至少对应一个校对规则

#### 选择

- 应用处理的语言种类多，选择Unicode字符集，MySQL选择UTF-8
- 应用中涉及已有数据的导入，若已有数据为GBK文字，选择UTF-8
- 数据库只需支持一般中文，数据量大，性能要求高，选择GBK
- 数据库需要做大量的字符运算，选择定长字符集
- 考虑客户端所使用的字符集编码格式

#### 设置

服务器字符集和校对规则

```sql
SHOW VARIABLES LIKE 'character_set_server'    --查询当前服务器字符集
SHOW VARIABLES LIKE 'collation_server' 		  --查看校对规则

mysqld -character-set-server=gbk              --启动时指定字符集为gbk
```

数据库字符集和校对规则

```sql
ALTER DATABASE									  --修改字符集
SHOW VARIABLES LIKE 'character_set_database'      --查询当前数据库字符集
SHOW VARIABLES LIKE 'collation_database' 		  --查看校对规则
```

表字符集和校对规则

```sql
ALTER TABLE					--修改字符集
SHOW CREATE TABLE 表名       --查看当前表的字符集和校对规则
```

列字符集和校对规则

针对相同的表不同字段需要使用不同字符集的情况

### MySQL数据库操作管理

#### 数据库

##### 创建

```sql
CREATE DATABASE [IF NOT EXISTS] db_name       --创建数据库
SHOW DATABASES								  --查看所有的数据库
SHOW CREATE DATABASE 数据库名称 \G			   --查看数据库详细信息
```

##### 修改

数据库名称可以进行修改，当存储引擎为MyISAM时，修改DATA目录下的库名文件夹，若存储引擎为InnoDB，无法修改库名

```sql
ALTER DATABASE [db_name]					
[DEFAULT CHARACTER SET charset_name] 		--修改数据库字符集
| [[DEFAULT] COLLATE collation_name]		--修改数据库校对规则

SHOW CREATE DATABASE db_name				--查看数据库详细信息
```

##### 删除

```sql
DROP DATABASE [IF EXISTS] db_name   		--删除数据库
```

