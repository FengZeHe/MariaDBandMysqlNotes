# MariaDB and Mysql Notes

## Mysql基础知识

- 其中有一个关键程序，就是服务器mysqld(d代表daemon，即守护进程；通常服务器都是守护进程)，MySQL和MariaDB的兽魂进程都叫mysqld。

- 给root设置初始密码

  ```
  mysqladmin -u root -p flush-privileges password "new_pwd"
  ```

#### 关于密码的更多问题

```
"SELECT User,Host FROM mysql.user;"
```

1. 如果运行不了，可能是PATH中没有mysql。那就要在前面加上/bin/或者/usr/bin，或者其他安装路径。MariaDB也是用这个命令。
2. 很多版本的Mysql都含有root和%的组合，其中%是指任何主机——这就不安全了，因为它允许人们从各个地方通过root连接MySQL。也有很多版本的Mysql含有空用户加locahost的组合，即任何用户都可以从localhost登录——这就是匿名用户。尽管用户已经存在，但初始密码是空的。需要做的是删掉没用的用户，并给留下的设置密码。尽管127.0.0.1和localhost对应的是同一台主机，但还需要分两次来修改密码。
3. 修改完成后使密码生效  `mysqladmin -u root -p flush-privileges`

#### 创建用户

``` mysql
"GRANT USAGE ON *.*
TO 'russell'@'localhost'
IDENTIFIED BY 'Rover#My_1st_Dog&Not_Yours!';"
```

- 创建了一个叫russell的用户，并允许从localhost登录Mysql。其中 `*.*`表示所有的数据库和所有表。

- 如果只想给用户查看权限

  ```
  "GRANT SELECT ON *.* TO 'russell'@'localhost';
  ```

- 赋予用户全部权限

  ```
  "GRANT ALL ON *.* TO 'russell'@'localhost';"
  ```

#### 连接到Mysql服务器

```
mysql -u root -p
```

1. `-p`选项后立即加上密码（不能填空格）
2. 如果MySQL用户名与操作系统相同，那么不需要加上-u选项，直接-p登录

#### Mysql开始的数据库

1. information_schema 数据库包含服务器相关信息
2. mysql 存储用户名、密码、权限
3. test 给使用者测试和练习



#### Select

```
select * from book WHERE status = 0 \G;
```

- `\G`是一种结尾。能让结果不以表格形式展示，而是每条记录多行展示。

```
mysql> select * from books where status = 0 \G;
*************************** 1. row ***************************
book_id: 100
  title: holy shit
 status: 0
1 row in set (0.00 sec)
```



#### Update

```
update book SET status = 1 where book_id =102;
```

- 可能遇到的问题

  ```
  You are using safe update mode and you tried to update a table without a WHERE that uses a KEY column.
  ```

- 解决办法

  ```
  SET SQL_SAFE_UPDATES = 0;
  ```

#### 习题
(1) 用mysql登录MySQL或MariaDB，并将默认数据库切换到test。
1. 建两个表，分别名为contacts和relation_types。这两个表的数字列都用INT 类型，字符列用 CHAR 类型。
2. 为CHAR指定一个你想要的最大长度，否则MySQL 会使用 CHAR 的极限长度，但那并不实用。注意该长度必须足够容纳你接下来要输入的数据
3. 如果你需要输入的内容既有数字又有字符（如电话号码中的连字符），那么使用CHAR。
4. contacts 表要有五个列：name、phone_work、phone_mobile、email、relation_id。
5. 而relation_types表只有两列: relation_id和relationship。建好着两个表后使用DESCRIBE语句看看它们的样子。

```
create table contacts (name CHAR(12),phone_work CHAR(12),phone_mobile CHAR(12),email CHAR(12),relation_id INT);
create table relation_types (relation_id INT,relationship CHAR(12));

describe test.contacts;
describe test.relation_types; 


```

(2)
1. 输入数据到刚才建的两个表中。首先是relation_types表，输入三行数据。第一列填成一位数的序列形式，第二列填文本：Family、Friend、Colleague.
2. 接着填contacts表，至少填5组虚构的名字、电话和邮件地址，而在最后的relation_id列，填入能与relation_types的relation_id对应的一个数，并确认那三个relation_id列都能被用到。
3. 执行两条select语句，以获得那两个表的所有列的所有数据，然后再写一条SELECT，要求只获取contacts中的姓名和邮件地址。
4. 用UPDATE来修改刚才输入的数据。首先修改某个人的命令和电话，接着修改某人的邮件地址以及（他）与你的关系(即relation_id)。要求两次修改部分只能用一条update语句。
5. 执行一条select语句，连接习题1的两个表。用JOIN字句来实现。因为两个表都有relation_id列，所以就在这两列上连接——写在WHERE字句中。
6. 只显示你的Friend的name和phone_mobile列——需要在WHERE中使用AND。先试试按relation_id来筛选，再试试relationship筛选。





