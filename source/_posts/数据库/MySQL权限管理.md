---
title: MySQL权限管理
author: 从此在回忆里
date: 2021-10-04 18:39:16
tags: 大学课程
categories: 数据库
---

### 权限系统

MySQL可以使用3种不同类型的安全检查

1. 登陆验证：用户名、密码验证
2. 授权：对用户设置其具体权限
3. 访问控制：是否允许操作

<!-- more -->

存储控制阶段：

1. 服务器检查是否允许连接
2. 若允许连接，服务器检查用户发出请求是否有相应的权限

服务器在存取控制阶段使用user、db、host表

在执行数据库操作时，需要通过root用户账号登录，对整个MySQL服务器具有完全控制







### 权限表

#### user表

user为MySQL中最终的一个权限表，可以使用`DESC`查看user的基本结构

user列分为：用户列、权限列、安全列、资源控制列

权限分为普通权限和管理权限：

> 普通权限：主要用于对数据库的操作
>
> 管理权限：主要用于对数据库进行管理的操作

用户连接时，权限表的存取过程：先从user表中的host、user、password判断来连接的ip、用户名称、密码是否存在，若存在，通过身份验证，否则拒绝连接

通过验证后，按照以下权限顺序得到数据库权限：user、db、table_priv、colums_priv，权限依次递减，全局覆盖局部

##### 用户字段

```sql
--查询user表的相关用户字段
SELECT Host ,User , 'Password' FROM mysql.'user'
```

##### 权限字段

权限字段以`priv`结尾，这些字段决定用户权限

权限字段的值只有`Y`或`N`，表示有权限和无权限，默认值为`N`，可以使用`GRANT`为用户赋予权限

```sql
--查看localhost主机下的用户的select、insert、update权限
SELECT SELECT_priv,insert_priv,update_priv,user,host
FROM mysql.user WHERE host = 'localhost';
```

##### 安全字段

安全字段：ssl_type        ssl_cipher 

​				   x509_issuer    x509_subject      

​					另外两个与授权插件有关

`ssl`可用于加密；`x509`标准可用于标识用户；`plugin`字段标识可用于验证用户身份的插件，若该字段为空，服务器使用内建授权机制验证

```sql
--查看hava_openssl是否具有ssl功能
SHOW VARIABLES LIKE 'hava_openssl';
```

##### 资源控制列

资源控制列用来限制用户使用的资源，有以下字段：

- `max_question`：用户每小时允许执行的查询操作次数
- `max_updates`：用户每小时允许执行的更新操作次数
- `max_connections`：用户每小时允许执行的连接操作次数
- `max_user_connections`：单个用户可以同时具有的连接次数

默认值都为0，表示无限制



#### db表和host表

db表存储用户对某个数据库的操作权限，决定用户能从哪个主机存取哪个数据库

host表存储某个主机对数据库的操作权限

这两权限表不受`GRANT`和`REVOKE`语句影响，可以使用`DESC`语句查看两表的基本结构

##### 用户列

db表的用户列：

- host：主机名
- db：数据库名
- uesr：用户名

host表的用户列：

- host：主机名
- db：数据库名

host为db的扩展，db表找不到host的值，需要到host表中寻找

##### 权限列

db表与host表的权限列大致相同，`create_routine_priv`和`alter_routine_priv`表示用户是否有创建和修改存储过程的权限

当希望用户只对某个数据库有操作权限，则将user表的对应权限设为`N`，在db表设置对应数据库的操作权限

##### tables_priv表

tables_priv表可以对单个表进行权限设置，用来指定表级权限，可以使用`DESC`语句查看两表的基本结构

tables_priv表字段：

- host：主机名
- db：数据库名
- uesr：用户名
- table_name：表名
- grantor：修改该记录的用户
- timestamp：修改该记录的时间
- table_priv：对表进行操作的权限，包括：select、insert、update、delete、create、drop、grant、reference、index、alter
- column_priv：对表中的列进行操作的权限，包括：select、insert、update、reference



#### columns_priv表

columns_priv表可以对表中的某一列进行权限设置

columns_priv表字段：

- host：主机名
- db：数据库名
- uesr：用户名
- table_name：表名
- column_name：指定对哪些数据列具有操作权限
- timestamp：修改该记录的时间
- column_priv：对表中的列进行操作的权限，包括：`select`、`insert`、`update`、`reference`



#### procs_priv表

procs_priv表可以对存储过程和存储函数进行权限设置，可以使用`DESC`语句查看两表的基本结构

procs_priv表字段：

- host：主机名
- db：数据库名
- uesr：用户名
- routine_name：存储过程或存储函数的名称
- routine_type：存储过程或存储函数的类型，该字段有两个值：`function`表示存储函数，`procedure`表示存储过程
- grantor：存储插入或修改该记录的用户
- proc_priv：拥有的权限，包括：execute、alter routine、grant
- timestamp：存储记录更新的时间

### 用户管理

```sql
--查看所有用户账号列表
USE mysql；
SELECT user FROM user；
```

#### 添加用户

新的MySQL数据库系统，只有一个root用户

##### 使用`CREATE USER`语句创建用户账号

```sql
CREATE USER<User_name> [IDENTIFIED BY <password>]
[LOGIN POLICY <policy_name>]
[FORCE PASSWORD CHANGE{ ON | OFF }]
--在MySQL服务器中添加新用户，用户名为zhangsan，主机名为localhost，口令设置为明文123
CREATE USER 'zhangsan'@'localhost' IDENTIFIND BY '123';
```

- `User_name`：指定创建用户账号，格式为：'user_name'@'host name'，user_name为用户名，host name为主机名
- `IDENTIFIED BY`：用于指定用户账号对应的口令，无口令可省略
- `policy_name`：指派给用户的登录策略的名称，无策略可省略
- `password`：为用户指定口令，无口令的用户不能连接数据库

用户ID和口令不能出现以下情况：

- 以空格、单引号、双引号开头
- 以空格结尾
- 含有分号

`CREATE USER`语句使用说明：

- 使用该语句，需要有MySQL数据库的`INSERT`权限或全局`CREATE USER`权限
- 使用该语句创建用户账号后，会在系统自身的user表添加一条新纪录
- 若两个用户具有相同的用户名和不同的主机名，MySQL会视为不同的用户，允许为两个用户分配不同权限
- 使用该语句未指定口令，MySQL允许该用户可以不使用口令登录（不推荐）
- 新创建的用户只允许进行不需要权限的操作，如查询所有存储引擎、字符串的列集等

##### 使用`INSERT`语句新建普通用户

使用`INSERT`语句需要有对user表的插入权限

使用`INSERT`语句至少要插入六个字段的值：host、user、password、ssl_cipher、x09_issuer、x509_subject

```sql
--插入xiaohong用户，主机名为localhost，密码时password(123)
INSERT INTO mysql.user(host,user,password,ssl_cipher,x09_issuer,x509_subject)
	VALUES('localhost','xiaohong',password(123),'','','');
	
--执行上述语句后，要使用’FLUSH PRIVILEGES'使命令生效，需要reload权限
FLUSH PRIVILEGES
```

##### 使用`GRANT`语句新建普通用户



#### 查看用户

```sql
SELECT *FROM mysql.user WHERE host='host_name' AND user='user_name'			--*表示所有列，也可以指定特定列
```



#### 修改用户账号

修改用户账号需要有MySQL数据库的`update`权限或全局`create user`权限

```sql
RENAME USER old_user TO new_user [,old_user TO new_user] ……     --old_user：系统中已存在的用户账号
																--new_user：新的MySQL用户账号
```



#### 修改用户口令

使用`mysqladmin`命令修改密码

```sql
mysqladmin -u username p password，根据提示先输入旧密码，在输入新密码
```

使用`set`语句修改

```sql
SET PASSWORD [FOR 'username'@'hostname']=password('new_password')   --不加FOR……，则修改当前用户密码
--修改xiaohong密码为 '123'
SET PASSWORD [FOR 'xiaohong'@'localhost']=password('123') 
```

修改user表，使用root登录，修改自己或普通用户密码

```sql
UPDATE mysql.user SET password=password('new_password') WHERE user='user_name' AND host='host_name'
--修改xiaohong的密码为 '123456'
UPDATE mysql.user SET password=password('123456') WHERE user='xiaohong' AND host='localhost'
```

每次修改后，需要使用`FLUSH PRIVILEGES`命令重新加载权限



#### 删除用户

使用`DROP USER`语句删除普通用户

```sql
DROP USER user[,user]……
--删除名为xiaohong用户，host值为localhost
DROP USER xiaohong@localhost
```

使用`DELETE`语句删除普通用户

```sql
DELETE FROM mysql.user WHERE user='user_name' AND host='host_name';
```

### 账户权限管理

#### 权限授予

```sql
GRANT priv_type[(column_list)][,priv_type[(column_list)]]……
ON[object_type] priv_level
TO user_sepcification[,user_sepcification]……
[WITH with_option]
--授予用户lili在数据库studentinfo的表student上拥有对列sno和列sname的select权限
GRANT select(sno,sname) ON student TO 'lili'@'localhost'
--不存在用户liming和用户huang，要求创建两个用户同时授予在数据库studentinfo的表student上拥有select和update权限
GRANT select，update ON studentinfo.student TO 
'liming'@'localhost' IDENTIFIED BY '123'
'haung'@'localhost' IDENTIFIED BY '789';
--授予用户lili可以在数据库studentinfo中执行所有操作的权限
GRANT ALL ON studentinfo.* TO 'lili'@'localhost';
```

GRANT 权限级别：

- *：当前数据库中的所有表
- \*.\*：所有数据库的所有表
- db_name.*：某个数据库中的所有表
- db_name.tbl_name：某个数据库的某个表或视图
- tbl_name：某个表或视图
- db_name.routine_name：某个数据库中的某个存储过程或函数

GRANT中priv_type值：

- `select`：授予用户可以使用`SELECT`语句访问特定表的权限
- `insert`：授予用户可以使用`INSERT`语句从一个特定表中添加数据行的权限
- `delete`：授予用户可以使用`DELETE`语句从一个特定表中删除数据行的权限
- `update`：授予用户可以使用`UPDATE`语句修改特定表中值的权限
- `reference`：授予用户可以创建一个外键来参照特定数据表的权限
- `create`：授予用户可以使用特定的名字创建一个数据表的权限
- `alter`：授予用户可以使用`ALTER TABLE`语句修改数据表的权限
- `index`：授予用户可以在表上定义索引的权限
- `drop`：授予用户可以删除数据表的权限
- `all`或`all privileges`：所有的权限

授予列权限时，只能使用`select`、`insert`、`update`，权限还要加上列名列表`colimn_list`



#### 权限的转移和限制

通过`GRANT`语句中使用`WITH`子句实现

转移权限

将`WITH`子句指定为`WITH GRANT OPTION`,表示`TO`子句中所指定的所有用户都具有把自己权限授予其他用户的权利

限制权限

若`WITH`子句中`WITH`关键字后为以下四项，即拥有限制权限

- `max_queries_per_hour count`：限制每小时可以查询数据库的次数
- `max_updates_per_hour count`：限制每小时可以修改数据库的次数
- `max_ccconnections_per_hour count`：限制每小时可以连接数据库的次数
- `max_user_connection count`：限制同时连接MySQL的最大用户数，`count`为用户数

```sql
--授予系统中的用户huang在数据库studentinfo的表student上每小时只能处理一条delete语句的权限
GRANT delete ON studentinfo.student TO 'huang'@'localhost' WITH max_queries_per_hour 1;
```



#### 权限的撤销

使用`REVOKE`语句，要有MySQL数据库的全局`CREATE user`权限或`UPDATE`权限

```sql
--回收某些特定的权限
REVOKE priv_type[(column_list)][,priv_type[(column_list)]]……
ON[object_type] priv_level
FROM user[,user]……
--回收特定用户的所有权限
REVOKE ALL PRIVILECES,GRANT OPTION
FROM user[,user]……
```

