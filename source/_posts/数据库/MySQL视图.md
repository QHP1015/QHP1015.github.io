---
title: MySQL视图
author: 从此在回忆里
date: 2021-10-04 18:38:27
tags: 大学课程
categories: 数据库
---

### 视图

视图是一个虚拟表，**不占据存储空间**，数据来源于数据库表或其他视图，当基本表发生变化时，视图的数据也会变化

当调用视图时，才会执行视图中的SQL语句，进行取数据操作

<!-- more -->

#### 视图的优势

- 增强数据安全性（不同的用户分配不同的视图）

- 提高灵活性，操作简单

- 提高数据的逻辑独立性

  > 使用视图可以向应用程序屏蔽表结构
  >
  > 使用视图可以向数据库表屏蔽应用程序







### 视图定义和管理

#### 创建与使用：

创建视图需要`CREATE VIEW` 的权限以及`SELECT` 权限

```sql
CREATE [ALGORITHM = {UNDEFINED | MERGE | TEMPTABLE}]
VIEW 视图名 [(视图列表)]						--视图名唯一
AS 查询语句									  
[WITH [CASCADED | LOCAL] CHECK OPTION] 		--CASCADED表示更新视图时要满足所有相关视图和表的条件，为默认值
											--LOCAL表示更新视图时，满足该视图本身的定义条件即可

--例：在表1上创建一个名为表1_view的视图，包含字段1，字段2，字段3
CREATE VIEW 表1_view(字段1,字段2,字段3) AS SELECT 字段1,字段2,字段3  FROM 表2，表3，表4 WHERE (条件)
--例：查询给定值的信息
SELECT *FROM 表1_view WHERE 字段1='给定值';
```

当向与视图关联的表中添加新字段，查询时该视图不包括新字段；若与视图关联的表或视图被删除，该视图不能使用



#### 删除

删除视图时，只能删除视图的定义，不会删除数据

```sql
DROP VIEW [IF EXISTS]
view_name[,view_name2]……
[RESTRICT | CASCADE]
```

一次可以删除多个视图



#### 查看

查看视图要有`SHOW VIEW` 的权限

查看方法：

```sql
DESCRIBE 视图名称;      或者DESC 视图名称;
SHOW TABLE STATUS LIKE '视图名'
SHOW CREATE VIEW '视图名'
--查询某数据库下的视图表
SELECT *FROM db_name.views WHERE table_name ='视图名'
```



#### 修改

```sql
CREATE OR REPLACE VIEW 视图名[{属性清单}] AS SELECT 语句 
ALTER VIEW 视图名[{属性清单}] AS SELECT 语句
```



#### 更新

通过视图进行插入(`INSERT`)、更新(`UPDATE`)、删除(`DELETE`)表中的数据

更新视图的语法与`UPDATE`语法一致
