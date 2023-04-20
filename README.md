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

```mysql
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


