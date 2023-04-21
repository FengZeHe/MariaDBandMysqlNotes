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

(2)

1. 输入数据到刚才建的两个表中。首先是relation_types表，输入三行数据。第一列填成一位数的序列形式，第二列填文本：Family、Friend、Colleague.
2. 接着填contacts表，至少填5组虚构的名字、电话和邮件地址，而在最后的relation_id列，填入能与relation_types的relation_id对应的一个数，并确认那三个relation_id列都能被用到。
3. 执行两条select语句，以获得那两个表的所有列的所有数据，然后再写一条SELECT，要求只获取contacts中的姓名和邮件地址。
4. 用UPDATE来修改刚才输入的数据。首先修改某个人的命令和电话，接着修改某人的邮件地址以及（他）与你的关系(即relation_id)。要求两次修改部分只能用一条update语句。
5. 执行一条select语句，连接习题1的两个表。用JOIN字句来实现。因为两个表都有relation_id列，所以就在这两列上连接——写在WHERE字句中。
6. 只显示你的Friend的name和phone_mobile列——需要在WHERE中使用AND。先试试按relation_id来筛选，再试试relationship筛选。

```
create table contacts (name CHAR(12),phone_work CHAR(12),phone_mobile CHAR(12),email CHAR(24),relation_id INT);
create table relation_types (relation_id INT,relationship CHAR(12));

describe test.contacts;
describe test.relation_types; 

insert into test.contacts VALUES('David','13333333','12222222','David@gmail.com',2);
insert into test.contacts VALUES('Mike','15555555','15666666','Mike@gmail.com',3);
insert into test.contacts VALUES('Jerker','1777777','19888888','Jerker@gmail.com',3);

select * from contacts;
select * from relation_types;

select name,email from contacts;
update test.contacts set email='Jerker@126.com' where name = 'Jerker';

update test.contacts set email = 'update2@126.com' , relation_id = 2 where name = 'David';

select name,phone_mobile,relationship from test.contacts join relation_types where contacts.relation_id = relation_types.relation_id;
```



## 数据库结构

#### 避坑

1. 很多新手喜欢在一个数据库中建立一个拥有很多列的大型表，但其实这样处理数据是很低效的。现实中仅建立一个表不合理；所以晴建立多个小表而非一个大表。
2. 建表时我们需要规划好方案，需要建什么域（或列），是有很多选项的。最起码要制定类型、包含字符还是整数？包含日期和时间、还是二进制数据？如何建立索引？

3. CHAR和VARCHAR的区别？CHAR分配到的空间总是最大长度，不会因为内容少而减少。使用哪种类型是一种权衡。用CHAR的话，存储引擎就能确切知道列表会返回多少东西，这会令表运行得更快，索引也更容易维护。而VARCHAR对硬盘空间需求更少，利用效率更高，这也是一种提高性能的手段。如果你能确定知道某列内容的长度，那就用CHAR,否则用VARCHAR。

#### 概念

#### blob

二进制大对象的意思，可以将一张图像文件、如jpge房间blob类。但这种做法通常是不好的，因为它会使得表变大从导致备份困难。更好的做法是将图像放在文件系统中，然后将其文件路径或url存放进数据库，以指示如何找到该文件。



### 习题

(1) 使用DROP TABLE语句将之前建的bird_orders表删掉。找回其建表语句。复制或键入文本编辑器然后进行修改:将 `brief_description `改成` TEXT `类型。注意不要留有多余的逗号。完成后，将改过的语句复制到 mysql 监视器上，并按 Enter 键来执行它。 如果报错，那就看看报错信息(可能不太明确)，再看看编辑器上的语句。检查一下改动了什么，有没有改错。确认关键字和值的位置正确，而且没有错别字。改完后再试着执行，直至成功为止。
(2) 之前提过，我们还可以为每种鸟存储更多相关方面的信息。但不要将这些数据放进 birds表，相反，再创建一个表，即参考表。用CREATE TABLE来创建，命名为`birds_wing_shapes`。建三个列。

 	1. 第一列名为 wing_id，类型为CHAR，最大长度是 2。设该列为 UNIQUE 键，但不带有 AUTO_INCREMENT。我们将手工输入两位代码来识别每行的数据—— 因为表中可能只有六行数据，所以手工输入是可以接受的。
 	2. 第二列叫 wing_shape，类型为CHAR，最大长度是 25，用于描述鸟翼类型(例如锥形)。
 	3. 第三列是 wing_example， BLOB 类型，用于存储该种鸟翼形状的图片。

(3)建完birds_wing_shapes表后，对它执行SHOW CREATE TABLE两次:一次以分号结尾， 一次以 \G 结尾。看看哪种样式的结果更具表现力。将返回的CREATE TABLE语句复制粘贴进文本编辑器。然后用`DROP TABLE`删掉`birds_wing_shapes`。
在编辑器中改改复制来的语句。首先是存储引擎，如果 ENGINE 的值不是 MyISAM，则改 成 MyISAM。接着，将字符集和校对方式分别改成 utf8 和 utf8_general_ci。 然后将改过的语句粘贴到 mysql 监视器上，并按 Enter 键来执行它。如果报错，那就看 看那迷惑人的报错信息，再看看你编辑器上的语句。检查一下改动了什么，有没有改 错。确认关键字和值的位置正确，而且没有错别字。改完后再试着执行，直至成功。一旦成功，就用 DESCRIBE 来查看该表长什么样。

(4) 再建两个类似 birds_wing_shapes 的表。一个用于存储关于鸟的身形的信息，另一个用 于存储关于鸟嘴形状的信息。它们会有助于观鸟爱好者找鸟。给它们分别起名为 birds_ body_shapes、birds_bill_shapes。
birds_body_shapes 表的第一列是 body_id，其类型为 CHAR(3)，并且是 UNIQUE 键。第二 列是 body_shape，类型为 CHAR(25)。第三列是 body_example，设为 BLOB 类型，以便存 放鸟的形状图像。
对 birds_bill_shapes 表，也建三个类似的列:类型为 CHAR(2)、作为 UNIQUE 键的 bill_id;CHAR(25) 类型的 bill_shape;存放鸟形图像的、BLOB 类型的 bill_shape。这 两个表的 ENGINE 都填 MyIASM，而 DEFAULT CHARSET 填 utf8，COLLATE 则填 utf8_ general_ci。都建完后，用 SHOW CREATE TABLE 检查一下你的工作

```
create table bird_orders (
	odder_id int auto_increment primary key,
    scientific_name varchar(255) unique,
    brief_description text,
    order_image blob
) DEFAULT  CHARSET=utf8 collate=utf8_general_ci;

create table birds_wing_shapers (
	wing_id char(2) unique,
	wing_shape char(25),
    wing_example blob
) 


```





