## mysql操作命令行

登录mysql

mysql -h localhost -u root -p

查看数据库

show databases;

查看test数据库中的表

use test

show tables;

## Mac 彻底删除mysql

执行下列命令

- sudo rm /usr/local/mysql
- sudo rm -rf /usr/local/mysql*
- sudo rm -rf /Library/StartupItems/MySQLCOM
- sudo rm -rf /Library/PreferencePanes/My*
- rm -rf ~/Library/PreferencePanes/My*
- sudo rm -rf /Library/Receipts/mysql*
- sudo rm -rf /Library/Receipts/MySQL*
- sudo rm -rf /var/db/receipts/com.mysql.*

其实不同的安装方式有些东西的存储位置不一样，删除完检查一下下面这些文件是否删除了，没有的话则删除掉：

- /usr/local/Cellar 里的mysql文件
- /usr/local/var 里的mysql文件
- /tmp 里的mysql.sock, mysql.sock.lock, my.cnf文件
- pid文件和err文件都在/usr/local/var/mysql里确保删除了
- brew安装的安装包存储在/usr/local/Library/Cache/Homebrew也可以一并删除
- 执行`brew cleanup`



用brew的话就是 brew install mysql



## 建立一个表

```

create table product2(
p_id int primary key,
p_name varchar(50),
f_id int,
foreign key (f_id) references users(user_id)
);
```

在netbeans中建立一个表，但是不能谁额



```
create table test 
( 
   name varchar(19), 
   id number, 
   value varchar(10), 
   primary key (name,id) 
) 
```

当表中只有一个主键时，它是唯一的索引；当表中有多个主键时，称为复合主键，复合主键联合保证唯一索引。

我们可以将多个字段设置为主键，形成复合主键，这多个字段联合标识唯一性，其中，某几个主键字段值出现重复是没有问题的，只要不是有多条记录的所有主键值完全一样，就不算重复。



将两个外键修改为联合主键

```
ALTER TABLE 表名 WITH NOCHECK ADD
CONSTRAINT [PK_表名] PRIMARY KEY NONCLUSTERED
(
[字段名1],
[字段名2]
)
```



## 时间类型

TIMESTAMP[(M)] 时间戳。范围是’1970-01-01 00:00:00’到2037年。



TIME 时间。范围是’-838:59:59’到’838:59:59’。



DATETIME 日期和时间的组合。 支持的范围是’1000-01-01 00:00:00’到’9999-12-31 23:59:59’。



## 修改删除，字段

更改Float字段类型to Decimal  

```
 ALTER TABLE 表名 MODIFY 字段名 decimal(10,2) not null default '0'；
```

```
  如：  ALTER TABLE invoice MODIFY TotalMoney decimal(10,2) not null default '0'；    
```

一、该列非主键、无default约束

直接更新：

alter table 表名 alter column 列名 数据类型

 

二、该列为主键列、无default约束

(1)删除主键

alter table 表名 drop constraint 主键名称

(2)更新数据类型

alter table 表名 alter column 列名 数据类型 not null

(3)添加主键

alter table 表名 add constraint 主键名称 primary key (主键字段1,主键字段2)

 

三、该列为主键列，有default约束

(1)删除主键

alter table 表名 drop constraint 主键名称

(2)解除default约束

USE 数据库名
IF EXISTS (SELECT name FROM sysobjects
WHERE name = 'default约束名' 
AND type = 'D')
BEGIN 
EXEC sp_unbindefault '数据表.字段'
END
GO

(3)更新数据类型

alter table 表名 alter column 列名 数据类型 not null

(4)添加主键

alter table 表名 add constraint 主键名称 primary key (主键字段1,主键字段2)

 

 

辅助语句：

(1)找出字段约束名称并赋值到变量中

declare @name varchar(50)
select  @name =b.name from sysobjects b join syscolumns a on b.id = a.cdefault 
where a.id = object_id('表名') 
and a.name ='列名'

(2)将字段绑定到用户自定义的数据类型，并不影响现有绑定（使用futureonly）

此示例将默认值 def_ssn 绑定到用户定义的数据类型 ssn。因为已指定 futureonly，所以不影响类型 ssn 的现有列。
USE 数据库名
EXEC sp_bindefault '列名', '自定义数据类型', 'futureonly'



## 外键和级联

外键必须建立索引； 外键绑定关系这里使用了“ ON DELETE CASCADE ” “ON UPDATE CASCADE”，意思是如果外键对应数据（外键关联的表中数据）被删除或者更新时，将本表关联数据完全删除或者相应地更新。

给一个已经创建的表增加外键



```
ALTER TABLE ORDERS ADD CONSTRAINT O_U FOREIGN KEY (MEMBER_ID) REFERENCES USERS(USER_ID);
```

解除外键关系

```
ALTER TABLE ORDERS_DETAIL drop CONSTRAINT RDERSDETAILORDERID;
```

derdy->

```
create table MYORDER  
(  
    ORDER_ID INTEGER NOT NULL GENERATED ALWAYS AS IDENTITY (START WITH 1, INCREMENT BY 1)primary key,  
    DATETIME TIMESTAMP,  
    PROCEEDS DOUBLE,
    MEMBER_ID INTEGER,
    foreign key (MEMBER_ID) references USERS(USER_ID)

)
```

