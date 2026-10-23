# PHP 基础 - MySQL


这是php基础中关于MySQL的内容。

## MySQL 创建数据库和表

安装好MySQL后，可以直接在命令行下操作MySQL，当然也可以使用PhpMyAdmin来进行数据的创建。

```bash
mysql -u root -p //连接到本地数据库，默认root用户没有密码
set password for root@localhost = password('xxx'); //设置root密码
show databases; //显示所有数据库

create database blog default charset utf8; // 创建blog数据库，并设置字符集为utf8
use blog; //设置当前数据库为blog
show tables; //显示当前数据库中的所有表
create table users( id int auto_increment, name varchar(50) not null, email varchar(100) not null, primary key(id));
describe users; //显示users的结构
```





## 插入和删除数据

```bash
insert into users values(null, 'zjl', 'ldxyzjl@163');
select * from users;
select email from users;
insert into users(name, email) values('zhao si', 'zhao@si.com');
select * from users order by name asc; //按名字升序返回记录
select * from users order by name desc; //按名字降序返回记录
insert into users(name, email) values('wang wu', 'wang@wu.com');
select * from users limit 2; //返回2条记录
delete from users where name = 'wang wu'; //删除名字为'wang wu'的记录
```





## 修改记录

```bash
//创建posts表，注意author_id是外键，会关联users表
create table posts(
    -> id int auto_increment,
    -> title varchar(150),
    -> body text,
    -> author_id int not null,
    -> primary key(id)
    -> );
insert into posts(title, body, author_id) values('My first post', 'the body of my post', 1);
update posts set author_id = 2 where id = 1; //将id为1的posts记录修改author_id为2
select * from posts inner join users on users.id = posts.author_id; //两个表的内连接查询
select title, body, name from posts inner join users on users.id = posts.author_id;

update posts set title = 'My updated post' where id = 1;
update posts set title = 'updated again', body = 'updated body' where id = 1;

alter table posts change title subject varchar(100); //将title列修改为subject
desc posts;
alter table posts change subject title varchar(150); //改名和类型
```





## 多表连接

```bash
create database practice;
use practice;
create table users(
    -> id int auto_increment primary key,
    -> username varchar(100)
    -> );
insert into users(username) values('zjl');
insert into users(username) values('zs');
insert into users(username) values('ww');

create table orders(
    -> id int auto_increment primary key,
    -> number int,
    -> user_id int
    -> );
insert into orders(number, user_id) values(1466227, 1);
insert into orders(number, user_id) values(7122567, 1);
insert into orders(number, user_id) values(3458212, 1);
insert into orders(number, user_id) values(9934832, 2);
insert into orders(number, user_id) values(354522, 2);

select * from users inner join orders on orders.user_id = users.id;
select users.id, username, number as oder_number from users inner join orders on orders.user_id = users.id;
select users.id, username, number as oder_number from users inner join orders on orders.user_id = users.id where id = 1;
ERROR 1052 (23000): Column 'id' in where clause is ambiguous //两个表都有id，不知道用哪一个。
select users.id, username, number as oder_number from users inner join orders on orders.user_id = users.id where users.id = 1;

select users.id, username, orders.number from users left join orders on users.id = orders.user_id;
select users.id, username, count(orders.number) as order_amount from users inner join orders on users.id = orders.user_id where users.id=1;
```





## php连接mysql

下面的描述已经过时，PHP不推荐用这种方式使用php，我在这里仅仅是说明一下。
创建`functions.php`文件

```php
<?php
//已经过时，不要使用
mysql_connect('localhost', 'root', '') or die('不能连接数据库'); //主机名，用户名，密码

mysql_select_db('practice');

$result = mysql_query('select * from users');

while ($row = mysql_fetch_object($result)) {
    // print_r($row);
    echo $row->username . '<br>';
}
```



如果我们要访问不同的数据库怎么办？我们还是结构化我们的代码，做一个函数。

```php
<?php
//已经过时，不要使用
function connect($host = 'localhost', $username = 'root', $password = '')
{
    return mysql_connect($host, $username, $password);
}

$conn = connect() or die('不能连接数据库');

mysql_select_db('practice', $conn);

$result = mysql_query('select * from users');

while ($row = mysql_fetch_object($result)) {
    echo $row->username . '<br>';
}
```



如果我们可以在连接的时候就传入数据库就更好了。

```php
<?php
//已经过时，不要使用
function connect($host, $username, $password, $database = '')
{
    $conn = mysql_connect($host, $username, $password);

    if (!empty($database)) {
        mysql_select_db($database, $conn);
    }

    return $conn;
}

$conn = connect('localhost', 'root', '', 'practice') or die('不能连接数据库');

$result = mysql_query('select * from users');

while ($row = mysql_fetch_object($result)) {
    echo $row->username . '<br>';
}
```



我们来继续结构化查询

```php
<?php
//已经过时，不要使用
function connect($host, $username, $password, $database = '')
{
    $conn = mysql_connect($host, $username, $password);

    if (!empty($database)) {
        mysql_select_db($database, $conn);
    }

    return $conn;
}

function query($query)
{
    $results = mysql_query($query);

    if ($results) {
        $rows = [];
        while ($row = mysql_fetch_object($results)) {
            $rows[] = $row;
        }
        return $rows;
    }

    return false;
}

$conn = connect('localhost', 'root', '', 'practice') or die('不能连接数据库');
$result = query('select * from users');

if ($result) {
    foreach ($result as $row) {
        echo $row->username . '<br>';
    }
}
```





## 使用PDO连接数据库

我们连接数据库的信息应该放置在一个配置文件中，而不是硬编码在函数库中。
创建`config.php`文件

```php
<?php

$config = [
    'DB_USERNAME' => 'root',
    'DB_PASSWORD' => '',
    'DB_HOST' => 'localhost',
    'DB_DATABASE' => 'practice'
];
```



创建`functions.php`

```php
<?php

require 'config.php';

try {
    $conn = new PDO("mysql:host={$config['DB_HOST']};dbname={$config['DB_DATABASE']}", $config['DB_USERNAME'], $config['DB_PASSWORD']);
    $conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
} catch (PDOException $e) {
    echo 'ERROR: ' . $e->getMessage();
}
```





## 查询数据

我们来添加查询的语句

```php
<?php

require 'config.php';

try {
    $conn = new PDO("mysql:host={$config['DB_HOST']};dbname={$config['DB_DATABASE']}", $config['DB_USERNAME'], $config['DB_PASSWORD']);
    $conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

    $results = $conn->query('select * from users');

    foreach ($results as $row) {
        print_r($row);
    }
} catch (PDOException $e) {
    echo 'ERROR: ' . $e->getMessage();
}
```



如果我们需要id为3的记录，我们可以这样

```php
$results = $conn->query('select * from users where id = 3');
```



如果id是动态获取的呢？我们来看一种错误的做法

```php
$id = 3;
$results = $conn->query('select * from users where id = ' . $id);
```



现在假设我们的id值被用户进行了注入攻击

```php
$id = '100 OR 1';
$results = $conn->query('select * from users where id = ' . $id);
```



数据已经全部暴露了，这个不得了。我们需要安全的做法

```plain
$results = $conn->query('select * from users where id = ' . $conn->quote($id));
```



但这仍然不是好的做法，下面我们来看一下好的做法。

## prepared statement

```php
$id = 3;
$stmt = $conn->prepare('select * from users where id = :id');
$stmt->execute([
    'id' => $id
 ]);

while ($row = $stmt->fetch()) {
    print_r($row);
}
```



现在可以尝试修改$id, 例如 `'3' '3 or 1'`

我们还可以这样做

```php
$stmt->bindParam(':id', $id, PDO::PARAM_INT);
$stmt->execute();
```



现在我们返回的是数组，我们可以返回对象

```php
while ($row = $stmt->fetch(PDO::FETCH_OBJ)) {
    print_r($row);
}

//或者
$stmt = $conn->prepare('select * from users where id = :id');
$stmt->setFetchMode(PDO::FETCH_OBJ);
```



也可以一次返回全部数据

```php
$stmt = $conn->prepare('select * from users');
$stmt->setFetchMode(PDO::FETCH_OBJ);
$stmt->execute();
$results = $stmt->fetchAll();
print_r($results);
```



使用prepared statement执行插入动作

```php
$stmt = $conn->prepare('insert into users(username) values(:username)');
$stmt->bindParam(':username', $username, PDO::PARAM_STR);

$username = 'aaa';
$stmt->execute();

$username = 'bbb';
$stmt->execute();
```



更简洁的方式是

```php
$stmt = $conn->prepare('insert into users(username) values(:username)');
$stmt->bindParam(':username', $username, PDO::PARAM_STR);

$users = ['ccc', 'ddd', 'eed'];
foreach ($users as $username) {
    $stmt->execute();
}
```





## PDO 辅助函数

我们先来创建`config.php`

```php
<?php

$config = [
    'DB_USERNAME' => 'root',
    'DB_PASSWORD' => '',
    'DB_HOST' => 'localhost',
    'DB_DATABASE' => 'practice'
];
```



创建`functions.php`

```php
<?php

require 'config.php';

function connect($config)
{
    try {
        $conn = new PDO("mysql:host={$config['DB_HOST']};dbname={$config['DB_DATABASE']}", $config['DB_USERNAME'], $config['DB_PASSWORD']);

        $conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

        return $conn;
    } catch (Exception $e) {
        return false;
    }
}
```



我们创建`index.php`进行测试

```php
<?php

require 'config.php';
require 'functions.php';

if (connect($config)) {
    echo 'Connected';
} else {
    echo 'Could not connect the db';
}
```



我们在辅助函数中增加一个`get`方法，能够获取某个表的全部数据，并能够返回数据，或者没有数据返回false

```php
function get($tableName, $conn)
{
    try {
        $result =  $conn->query("SELECT * FROM $tableName");
        
        return ($result->rowCount > 0) ? $result : false;
    } catch (Exception $e) {
        return false;
    }
}
```



我们来测试一下`index.php`

```php
<?php

require 'config.php';
require 'functions.php';

$conn = connect($config);

if ($conn) {
    $result = get('users', $conn);
    if ($result) {
        foreach($result as $row) {
            print_r($row);
        }
    } else {
        echo 'No rows';
    }
} else {
    echo 'Could not connect the db';
}
```



我们也可以创建一个普通的`query`函数，`functions.php`

```php
function query($query, $bindings, $conn)
{
     $stmt = $conn->prepare($query);
     $stmt->execute($bindings);
     $result = $stmt->fetchAll();
     
     return $result ? $result : false;
}
```



测试

```php
if ($conn) {
    $rows = query('select * from users where id = :id', [
        'id' => 1
        ], $conn);
    if ($rows) {
        print_r($rows);
    } else {
        echo 'No data';
    }
} else {
    echo 'Could not connect the db';
}
```





## 命名空间

我们的函数库命名会出现问题，当我们引入其他的库的时候。我们需要命名空间来进行隔离。

```php
<?php namespace Zjl\DB;

function connect()
{
    return 'conncted';
}
```



这样，我们的`connect`就在`Zjl\DB`这个命名空间下了。我们测试一下

```php
<?php

require 'functions.php';

// echo connect(); //无法找到
echo Zjl\DB\connect();
```



但是我们不想每次都输入那么长的名字，我们可以使用`use`关键字

```php
<?php

require 'functions.php';

use Zjl\DB;

echo DB\connect();
```


---

> 作者: [张景林](https://zhangjinglin.github.io/about/)  
> URL: https://zhangjinglin.github.io/2016/03/php-fundamentals-mysql/  

