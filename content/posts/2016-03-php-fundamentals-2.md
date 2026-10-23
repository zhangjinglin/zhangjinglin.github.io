---
title: "PHP 基础(二)"
date: 2016-03-17T02:09:04+00:00
url: "/2016/03/php-fundamentals-2/"
draft: false
---

这是php基础的第二篇。

## $_GET

我们如何在页面之间传送变量？或者如何获取到用户的输入变量呢？我们可以使用`查询字符串QueryString`来解决这个问题。

```html
<?php
var_dump($_GET);
```



`$_GET`是php提供的超级全局变量，用于查询字符串的处理，我们可以在地址栏输入查询变量来获取相应的用户输入。

```plain
http://locahost/index.php?name=zjl
//显示结果
// array(1) { 'name' => string(3) "zjl" }
```



如果需要多个变量，使用`&`来作为连接符号。

```plain
http://locahost/index.php?name=zjl&job=teacher
//显示结果
// array(2) { 'name' => string(3) "zjl" 'job' => string(7) "teacher" }
```



所以我们就可以这样来处理页面了

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Super Global</title>
</head>
<body>
    <h1>Super Global</h1>
    <?=$_GET['name']?>
</body>
</html>
```



上面的页面有一个问题，如果我们没有提供查询字符串，那么会导致错误，所以我们要在使用之前进行测试。

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Super Global</title>
</head>
<body>
    <h1>Super Global</h1>
    <?php if (isset($_GET['name'])): ?>
    <p>Welcome you, <?=$_GET['name']?></p>
    <?php else: ?>
    <p>Nobody</p>
    <?php endif ?>
</body>
</html>
```



在处理查询字符串的时候我们要避免注入攻击，我们来尝试一下这样输入

```plain
http://localhost/index.php?name=<h1>zjl</h1>
//看看结果
```



我们需要使用`htmlspecialchrs`，在[php.net](http://php.net)网站中查看一下。

```html
<p>Welcome you, <?=htmlspecialchars($_GET['name'])?></p>
```



在页面之间传送信息也是可以的

```html
<a href="about.php?name=zjl">About</a>
```





## $_POST

我们来看一下表单的处理

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Post</title>
</head>
<body>
    <h1>Contact Form</h1>
    <form action="" method="get">
        <label for="name">Name:</label>
        <input type="text" name="name"/>
        <input type="submit" value="Submit"/>
    </form>
</body>
</html>
```



上面表单的`action`为空，意味着表单的提交会提交到当前文件，`method`采用get方式，输入信息提交表单，查看一下地址栏，你会发现表单的提交信息出现在了地址栏，这就是`get`提交方式。

将`method`修改为`post`再次提交，你会返现信息消失了，我们如何处理post数据呢？

```html
<?php
print_r($_POST);
?>

<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Post</title>
</head>
<body>
    <h1>Contact Form</h1>
    <form action="" method="post">
        <label for="name">Name:</label>
        <input type="text" name="name"/>
        <input type="submit" value="Submit"/>
    </form>
</body>
</html>
```



php使用`$_POST`超级全局变量来处理`post`请求。

但是如果我们希望在没有提交表单的时候不显示输出信息，怎么处理呢？我们修改成这样

```html
<?php
if (!empty($_POST)) {
    print_r($_POST);
}
?>
```



不过，更常见的形式是使用`$_SERVER['REQUEST_METHOD']`来获取当前是get请求还是post请求。

```html
<?php
if ($_SERVER['REQUEST_METHOD'] == 'POST') {
    print_r($_POST);
}
?>
```



我们修改表单

```html
<?php
if ($_SERVER['REQUEST_METHOD'] == 'POST') {
    print_r($_POST);
}
?>

<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Post</title>
</head>
<body>
    <h1>Contact Form</h1>
    <form action="" method="post">
        <label for="name">Name:</label>
        <input type="text" name="name"/>
        <br>
        <label for="email">Email:</label>
        <input type="text" name="email"/>
        <br>
        <label for="message">Message:</label>
        <input type="text" name="message"/>
        <br>
        <input type="submit" value="Submit"/>
    </form>
</body>
</html>
```




> 对于输入字段来说，必须存在`name`属性，你可以去掉某个字段的name属性，提交一下，查看结果。

## Session

存储在服务器端的用户数据，每个用户都有不同的session。

我们来写一个`index.php`文件

```html
<?php
//必须出现在页面的开始，前面不能有html
session_start();

$_SESSION['username'] = 'zjl';

?>
<a href="about.php">About</a>
```



再来`about.php`

```html
<?php
session_start();

echo $_SESSION['username'];
```



可以看出来，在index.php中放在`session`中的数据是可以获取到的，如果你关闭浏览器，再次直接访问`about.php`，什么信息也不会看到，因为新开浏览器相当于一个新的会话，session中是没有数据的。

当session数据不再需要时，比如用户已经选择登出，可是使用`session_destroy()`来销毁。

```php
<?php
session_start();

echo $_SESSION['username'];

session_destroy();

//即使使用了session_destroy, session中的数据在当前页面还是存在
$_SESSION = [];

print_r($_SESSION);
```



我们来看一下一个登陆的简单例子

```html
<?php
if ($_SERVER['REQUEST_METHOD'] == 'POST') {
    echo 'posted';
}
?>

<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Login</title>
</head>
<body>
   <form action="" method="post">
       <input type="text" name="name"/>
       <input type="password" name="password">
       <input type="submit" value="Submit"/>
   </form>
</body>
</html>
```



这是一个我们已经熟悉的形式，不过我们还是可以修改一下

```html
<?php
if (isset($_POST['loginForm'])) {
    echo 'posted';
}
?>
...
<input type="submit" value="Submit" name="loginForm"/>
```



我们为提交按钮添加一个名字，然后判断$_POST中是否包含提交按钮的信息来确定是否是提交。这样的好处是如果页面中包含多个表单的话，我们也可以区分出来是哪一个表单的提交。

我们来完整的实现登录流程，现在不涉及数据库，所以我们把用户和密码硬编码在程序中。

`index.php`代码：

```html
<?php
session_start();

define('USERNAME', 'zjl'); //定义常量，常量一般大写
define('PASSWORD', '123');

if (isset($_POST['loginForm'])) {
    $username = $_POST['name'];
    $password = $_POST['password'];
    if ($username === USERNAME && $password === PASSWORD) {
        $_SESSION['username'] = $username;
        header("Location: admin.php");
    } else {
        $status = "Username or Password Incorrect.";
    }
}
?>

<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Login</title>
</head>
<body>
   <form action="" method="post">
       <input type="text" name="name"/>
       <input type="password" name="password">
       <input type="submit" value="Submit" name="loginForm"/>
   </form>
   <?php if (isset($status)): ?>
   <p><?=$status?></p>
   <?php endif ?>
</body>
</html>
```



`admin.php`的代码：

```html
<?php
// 防止用户直接访问页面，需要先判断是否有登录成功信息
session_start();
if (!isset($_SESSION['username'])) {
    header("Location: index.php");
}
?>
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Admin</title>
</head>
<body>
    <h1>Secret admin area.</h1>
    <h2>Welcome <?=$_SESSION['username']?></h2>
    <a href="logout.php">Logout</a>
</body>
</html>
```



`logout.php`代码

```html
<?php
session_start();
session_destroy();
$_SESSION = [];
header('Location: index.php');
```





## 重构

我们的登录案例已经能够工作了，但是我们的代码是分散在各个文件中的，这不利于我们未来的开发，我们需要组织我们的代码更加结构化。

首先考虑我们的管理页面可能有多个页面，如果我们在每一个页面的前面都加上判断的话，一旦发生修改我们就崩溃了。所以将判断是否是登录用户的功能抽取出来，放在函数文件中。创建一个`function.php`的文件，

```php
<?php

function is_logged_in()
{
    if (!isset($_SESSION['username'])) {
        header("Location: index.php");
        die();
    }
}
```



我们需要修改`admin.php`以便引入我们的函数库。

```php
<?php
session_start();
include 'function.php';
is_logged_in();
?>
```



现在直接访问`admin.php`我们仍然回到了登录页面，good！

但是现在我们的函数是一个硬编码的跳转，我们可以修改它更加通用一些

```php
<?php

function is_logged_in()
{
    session_start();
    if (!isset($_SESSION['username'])) {
        return false;
    } else {
        return true;
    }
}
```



修改`admin.php`

```php
<?php
session_start();
include 'function.php';

if (!is_logged_in()) {
    header("Location: index.php");
    die();
}
```



这样就好多了。但是我们的函数仅仅是根据变量是否存在而返回值，简化一下

```php
function is_logged_in()
{
    return isset($_SESSION['username']);
}
```



Bingo!

在`index.php`中，我们的用户民和密码是直接写在页面中的，不过更好的解决方法是将这些信息写在配置文件中。创建`config.php`

```php
<?php
define('USERNAME', 'zjl'); //定义常量，常量一般大写
define('PASSWORD', '123');
```



修改`index.php`

```php
<?php
include 'config.php';

session_start();

if (isset($_POST['loginForm'])) {
    ...
}
?>
```



我们来重构登录方法，`function.php`

```php
function validate_user_creds($username, $password)
{
    return ($username === USERNAME && $password === PASSWORD);
}
```



修改`index.php`

```php
<?php
include 'config.php';
include 'function.php';

session_start();
if (isset($_POST['loginForm'])) {
    $username = $_POST['name'];
    $password = $_POST['password'];
    if (validate_user_creds($username, $password)) {
        $_SESSION['username'] = $username;
        header("Location: admin.php");
    } else {
        $status = "Username or Password Incorrect.";
    }
}
```



你可能看起来这样重构没什么太大的改变，但是考虑到将来我们的验证函数可能从数据库中读取信息或者是通过OAuth来进行认证，但是我们的登录页面是不关心的了，这样就进行了逻辑上的隔离。

## Cookie

cookie是存储在客户端（浏览器）中的数据。

```php
<?php

setcookie('fontSize', 25);
```



在浏览器中浏览，然后检查一下浏览器（Inspect Element）中的信息。OK，现在我们删除cookie，然后改编代码

```php
<?php

setcookie('fontSize', 25);

echo $_COOKIE['fontSize'];
```



刷新浏览器，我们会遇到错误，没关系，这是因为现在cookie还不存在，再次刷新浏览器，就可以看见结果了。

我们可以利用cookie来记录一些信息。

```html
<?php
setcookie('fontSize', 25);
?>
<html>
    <head>
        <title>Cookie Sample</title>
        <style>
            body {
                font-size: <?=$_COOKIE['fontSize']?>;
            }
        </style>
    </head>
    <body>
        <p>Hi there, how are you?</p>
    </body>
</html>
```



现在我们就可以看到页面依据cookie的样式了。如果我们检查cookie信息，会发现cookie的生命周期是session，意味着关闭浏览器cookie就失效了。

```php
setcookie('fontSize', 25，time() + 60 * 60); //设定超时1小时
```



怎么删除cookie呢？就是设置cookie时间为过去就好了

```php
setcookie('fontSize', 25, time() - 60 * 60);
```



我们来看一个完整的例子

```php
<?php
if ($_SERVER['REQUEST_METHOD'] == 'POST') {
    setcookie('fontSize', (int)$_POST['fontSize'], time() + 60 * 60);
    header('Location: index.php');
}

?>
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Fonts Sample</title>
    <style>
        p {
            font-size: <?=isset($_COOKIE['fontSize']) ? $_COOKIE['fontSize'] : '16'?>px;
        }
    </style>
</head>
<body>
    <form action="" method="post">
        <select name="fontSize">
            <option value="10">10</option>
            <option value="20">20</option>
            <option value="30">30</option>
            <option value="40">40</option>
        </select>
        <input type="submit" value="ok">
    </form>

    <h1>My Page</h1>
    <p>Lorem ipsum dolor sit amet, consectetuer adipiscing elit,
    sed diam nonummy nibh euismod tincidunt ut laoreet dolore
    magna aliquam erat volutpat. Ut wisi enim ad minim veniam,
    quis nostrud exerci tation ullamcorper suscipit lobortis nisl
    ut aliquip ex ea commodo consequat. Duis autem vel eum iriure
    dolor in hendrerit in vulputate velit esse molestie consequat,
    vel illum dolore eu feugiat nulla facilisis at vero eros et
    accumsan et iusto odio dignissim qui blandit praesent luptatum
    zzril delenit augue duis dolore te feugait nulla facilisi.
    Nam liber tempor cum soluta nobis eleifend option congue
    nihil imperdiet doming id quod mazim placerat facer possim
    assum. Typi non habent claritatem insitam; est usus legentis
    in iis qui facit eorum claritatem. Investigationes
    demonstraverunt lectores legere me lius quod ii legunt saepius.
    Claritas est etiam processus dynamicus, qui sequitur mutationem
    consuetudium lectorum. Mirum est notare quam littera gothica,
    quam nunc putamus parum claram, anteposuerit litterarum formas
    humanitatis per seacula quarta decima et quinta decima. Eodem
    modo typi, qui nunc nobis videntur parum clari, fiant sollemnes
    in futurum.</p>
</body>
</html>
```





## 创建一个网站计数器

我们通过一个文件来保存网站的访问者计数。创建一个文件，`index.tmp.php`作为一个我们需要的模板文件，别紧张，除了文件名以外没有什么特殊的地方，它会接收控制器传送来的数据并显示。

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>My Website</title>
</head>
<body>
    <h1>Welcome, my website!</h1>
    <p>You are the X visitor to this website.</p>
</body>
</html>
```



下面是`index.php`的代码

```php
<?php

function set_count($file_name = 'counter.txt')
{
    if (file_exists($file_name)) {
        //读取值
        $handler = fopen($file_name, 'r');
        //增加1
        $count = (int)fread($handler, 20) + 1; //20是我们自行设定的一个长度

        //写入新值
        $handler = fopen($file_name, 'w');
        fwrite($handler, $count);
        fclose($handler);

    } else {
        //创建文件
        $handler = fopen($file_name, 'w+');
        $count = 1;
        //写入初始值
        fwrite($handler, $count);
        fclose($handler);
    }

    return $count;
}

$site_count = set_count();

require 'index.tmp.php';
```



修改`index.tmp.php`显示计数器

```html
<p>You are the <?=$site_count?> visitor to this website.</p>
```



php提供了`file_get_contents`和`file_put_contents`函数来简化我们的工作

```php
function set_count($file_name = 'counter.txt')
{
    if (file_exists($file_name)) {
        $count = (int)file_get_contents($file_name) + 1;
        file_put_contents($file_name, $count);
    } else {
        $count = 1;
        file_put_contents($file_name, $count);
    }

    return $count;
}
```



php提供的`file`函数能够将文件内容作为数组一次读出，将`counter.txt`修改为

```plain
5
10
15
20
```



尝试下面的语句

```php
print_r(file($file_name)); //文件内容按照数组输出
```





## 文件操作

```php
<?php

//寻找与模式匹配的文件路径
$files = glob('r*.txt');
print_r($files); //['random.txt', 'really-cool-file.txt']

//所有的jpg文件
$images = glob('img/*.jpg');

//所有的jpg，png文件
$images = glob('img/*.{png,jpg,jpeg}', GLOB_BRACE); //千万不要在类型前面有空格

foreach ($images as $img) {
    echo $img;
    echo '\r';

    echo basename($img); //仅仅是文件名，不含目录名
    echo dirname($img); //目录名

    echo substr($img, 4); //略过前面四个字符
    echo substr($img, -3); //后面的三个字符

    print_r(pathinfo($img));

    echo pathinfo($img, PATHINFO_EXTENSION);
    echo pathinfo($img, PATHINFO_FILENAME);
}
```





## 基于文件系统的电子邮件注册系统

我们准备写一个能够让用户注册的邮件列表系统。首相创建`index.tmp.php`模板文件。

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Email Registration</title>
</head>
<body>
    <h1>Join the Mailing List</h1>

    <form action="" method="post">
        <label for="name">Your Name:</label>
        <input type="text" name="name"/>
        <br>
        <label for="email">Your email address:</label>
        <input type="text" name="email"/>
        <br>
        <input type="submit" name="Sign Up!"/>
    </form>
</body>
</html>
```



创建`index.php`控制器文件

```plain
<?php

require 'index.tmp.php';
```



下面我们来处理表单。

```php
<?php

if ($_SERVER['REQUEST_METHOD'] == 'POST') {
    $name = $_POST['name'];
    $email = $_POST['email'];
}

require 'index.tmp.php';
```



避免用户输入的信息含有空格

```php
$name = trim($_POST['name']);
$email = trim($_POST['email']);
```



用户什么也没输入怎么办？继续处理

```php
if ($_SERVER['REQUEST_METHOD'] == 'POST') {
    ...
    if (empty($name) || empty($email)) {
        $status = 'Please provide a valid name and email';
    }
}
```



在`index.tmp.php`中添加显示信息。

```php
<h1>Join the Mailing List</h1>
<?=isset($status) ? $status : ''?>
...
```



如果输入合法，我们可以来进行用户登录的处理，但最好新建一个`functions.php`文件来处理一些公共的函数，以避免我们的页面太过复杂。

```php
<?php

function add_registered_user($name, $email)
{
    file_put_contents('mailing_list.php', "$name: $email\n", FILE_APPEND); //避免用户可以直接访问，应该使用'../mailing_list.php'以保证安全

}
```



修改`index.php`

```php
if (empty($name) || empty($email)) {
    $status = 'Please provide a valid name and email';
} else {
    add_registered_user($name, $email);
    $status = 'Thank for registration.';
}
```



如果用户输入的信息有问题，那么再次返回的时候，用户的信息就已经消失了，真不是一个好方法。修改`index.tmp.php'`

```html
<input type="text" name="name" value="<?=isset($_POST['name']) ? $_POST['name'] : ''?>"/>
<input type="text" name="email" value="<?=isset($_POST['email']) ? $_POST['email'] : ''?>"/>
```



这样是不错的，但是我们的视图是不应该处理这么多的逻辑的。修改为

```html
<input type="text" name="name" value="<?=old('name')?>"/>
<input type="text" name="email" value="<?=old('email')?>"/>
```



我们添加函数

```php
function old($key)
{
    if (!empty($_REQUEST[$key])) { //$_REQUEST 可以处理$_GET或者$_POST中的数据
        return htmlspecialchars($_REQUEST[$key]);
    }

    return '';
}
```



现在我们的登录系统不能处理任何的合法性验证，我们来处理验证函数

```plain
function valid_email($email)
{
    // return preg_match('/regular expression/', $email);
    return filter_var($email, FILTER_VALIDATE_EMAIL);
}
```



在`index.php`中添加验证

```php
if (empty($name) || empty($email) || !valid_email($email)) {
...
```



现在我们来完成显示列表的工作。在`functions.php`中添加读取用户的功能。

```php
define('MAILING_LIST', 'mailing_list.php');

function add_registered_user($name, $email)
{
    file_put_contents(MAILING_LIST, "$name: $email\n", FILE_APPEND); //避免用户可以直接访问，应该使用'../mailing_list.php'以保证安全

}

function get_registered_users($path = MAILING_LIST)
{
    $users = file($path);

    if (count($users)) {
        return array_map(function($user) {
            return explode(': ', htmlspecialchars($user));
        }, $users);
    }

    return false;
}
```



创建`registered_user.tmp.php`作为模板

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <!--确保安全性-->
    <h1>Folks On Your Mailing List</h1>
    <?php if ($registered_users): ?>
        <ul>
            <?php foreach ($registered_users as $user): ?>
                <?php list($name, $email) = $user; ?>
                <li><?=$name?>: <a href='mailto: <?=$email?>'><?=$email?></a></li>
            <?php endforeach ?>
        </ul>
    <?php else: ?>
        <p>There are no registered users.</p>
    <?php endif ?>
</body>
</html>
```



创建`registered_users.php`作为控制器。

```php
<?php
require 'functions.php';

$registered_users = get_registered_users();

require 'registered_users.tmp.php';
```
