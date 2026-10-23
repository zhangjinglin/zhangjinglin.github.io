---
title: "PHP 基础(一)"
date: 2016-03-15T05:52:48+00:00
url: "/2016/03/php-fundamentals/"
draft: false
---

## PHP安装

Mac：安装MAMP
Windows：安装XAMPP
Linux：独立安装各个组件

## 变量

我们来写一个基本的HTML

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>My Website</title>
</head>
<body>
    <h1>My Website</h1>
</body>
</html>
```



在HTML h1后面增加一个PHP的输出语句，

```php
<?php echo 'Hello, World'; ?>
```



在浏览器中查看结果。对服务器来说，所有的HTML标记会原封不动的发送到客户端，但是一旦遇到了`<?php`的
开始标记，就会交给PHP处理程序进行处理，然后将结果发送回浏览器。在浏览器中查看源代码，我们只会看见
PHP的处理结果，而不会看见php的语句。

对php来说，我们也可以使用`print`来进行输出，不过使用`echo`在php的世界中更常用一些。字符串可以使用
单引号或者双引号。

```php
<?php
    $name = 'Zhang Jinglin';
    echo $name;
?>
```



php中的变量使用`$`开始作为变量开始。
我们来看一下单引号和双引号之间的区别。

```php
$name = 'zhangjinglin';
echo "Hello, $name"; //Hello, zhangjinglin
echo 'Hello, $name'; //Hello, $name
```



对php来说，双引号字符串中的变量是可以被解析的，单引号中的变量不被解析，直接原样输出。对于
不含变量的字符串来说，单双引号没有区别。

php使用`.`作为字符串连接符。

```php
echo 'hello, ' . $name;
echo "hello, " . $name;
```



上面的语句结果相同。

php是和HTML结合在一起使用的。

```php
<?php
    $name = 'zhangjinglin';
?>
<p>Hello, <? echo $name; ?></p>
```



一定要注意上面的`<?php`与`?>`的成对出现。

如果我们只是想输出一个变量，而不是执行一个语句，可以使用php的简洁写法。

```php
<p>Hello, <?=$name?></p>
```



当然也可以直接使用php来输出与HTML的结合。

```php
<?php
$name = 'zhangjinglin';
echo "<p>Hello, $name</p>";
?>
```





## 显示错误信息

下面的语句会出项错误，但是我们什么也看不到

```php
<?
echo hello, world!;
?>
```



在php.ini的配置中，我们需要设定：

```ini
display_errors = On
```



当然，这个应该在开发环境中，在生产环境中需要设置为`Off`. 在`XAMPP`中，默认为`On`，但在其他环境中，
可能需要自行设定。

## 数组

比如我们想定义12个月的变量，当然可以使用`$jan $feb ...`去定义，不过最好的方式是使用数组。

```php
$months = array('januara', 'february', 'march', 'april', 'may');
var_dump($months);
```



`var_dump`是用于输出数组的函数，不能使用`echo`输出数组。

```php
echo $months[4]; //may
echo $months[0]; //januara
```



也可以使用php的数组简写定义方式(php 5.4 以上版本)，和`print_r`来输出数组。

```php
$months = ['januara', 'february', 'march', 'april', 'may'];
print_r($months);
```



在php中输出数组与html结合的方式。

```php
<ul>
<?php
    $months = ['januara', 'february', 'march', 'april', 'may'];
    foreach ($months as $month) {
        echo "<li>$month</li>";
    }
?>
</ul>
```



php也可以定义在不同的位置上，我们在文件头部定义变量，这次定义一个关联数组。

```php
<?php
    $sites = array(
        'baidu' => 'www.baidu.com',
        'google' => 'www.google.com',
        'bing' => 'www.bing.com'
    );
?>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>My Website</title>
</head>
<body>
    <ul>
    <?php foreach($sites as $name => $url) {
        echo "<li>$name, $url</li>";
    } ?>
    </ul>
</body>
</html>
```



我们也可以更进一步，将输出改为：

```php
echo "<li><a href='$url'>$name</a></li>";
```



php也提供了短写的写法，能够简化`foreach if`的语句复杂度，修改上面的语句为

```php
<ul>
<?php foreach($sites as $name => $url): ?>
    <li>
    <a href="<?=$url?>"><?=$name?></a>
    </li>
<?php endforeach ?>
</ul>
```



这样，写起来会舒服一些。同样，`if`的写法：

```php
<?php if (xxx): ?>
//html
<?php else: ?>
//html
<?php endif ?>
```



`array_push($arr, $val)` 向数组中添加数据，更简单的方法是`$arr[] = $val`。
对关联数组来说，使用`$arr[$key] = $val`。
`array_pop($arr)`删除最后一个数据，并返回删除数据。
`array_shift($arr)`删除开始的数据，并返回删除数据。
`array_unshift($arr, $val)` - 在数组开头插入一个或多个单元

在[php.net](http://php.net)中可以搜索所有的函数。

查看`array_filter()`函数，并掌握用法。

## 条件语句

`if`语句的格式

```php
<?php
if() {
    //do something
} esleif() {
    //do other
} else {
    //do something else
}
```



一个简单的例子

```php
<?php
$name = 'zjl';

if(strlen($name) == 3) {
    echo 'Your name length is 3';
} else {
    echo 'Your name length is not 3';
}
```



php中的判等有两种方式，注意类型

```php
'3' == 3 // true
'3' === 3 // false
```



php中认为`0, false`都是假值，`1, true, 已定义的变量`都是真值。

```php
<?php
$name = 'zjl';
$val = 0;
if ($name) echo 'true'; //true
if ($val) echo 'true'; //false
```



请自行了解`switch`。

可以使用`isset`来判断变量是否定义。

```php
<?php
echo isset($name) ? "Hello, $name" : 'Noname';
```





## 循环

使用`foreach`来遍历数组。

```php
foreach($arr as $item) {
...
}

foreach($arr as $key => $val) {
...
}
```



`for`语句也可以使用。

```php
for ($i = 0; $i < count($arr); $i++) {
...
}
```





## 文件包含

在实际的工作中，我们可能有一些文件的很多内容是相同的，比如说多个页面都有相同的页面布局，
或者我们需要一些公共的函数库，这就可以使用PHP的文件包含功能。

比如我们创建一个文件，叫做`header.php`

```php
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Include</title>
</head>
<body>
    <header>
        <h1>My Website</h1>
    </header>
```



这个文件包含了HTML模板头部，可以在多个文件中使用。

我们再创建一个`footer.php`文件，包含模板的尾部。

```html
    <footer>
        <p>Copyright Infomation</p>
    </footer>
</body>
</html>
```



现在我们就可以定义多个文件来使用了。创建文件`index.php`。

```
<?php include 'header.php'; ?>
    <div class="container">
        <p>Main content.</p>
    </div>
<?php include 'footer.php'; ?>
```



再创建一个`about.php`文件，也使用包含文件

```
<?php include 'header.php'; ?>
    <div class="container">
        <p>About content.</p>
    </div>
<?php include 'footer.php'; ?>
```



可见，我们简化了很多的工作。

出了可以使用`include`也可以使用`require`来进行文件包含，两者的区别是，include如果没有找到文件
会发出警告并继续执行，而require会出现错误并停止执行。
还有一个`require_once`能够保证仅仅包含一次文件，即使你写了多个。比如你写了一个类B文件，依赖
另外一个类A，有可能其他的类C也依赖类A，当同时使用类B和类C就可能引入两次类A，所以使用`require_once`
就是一个好的选择。

## 函数和变量作用域

在php中使用`function`关键字来定义函数，函数可以包含参数和返回值。

```php
<?php
function say_hello()
{
    return 'Hi, there';
}

echo say_hello();
```



当然我们也可以存储函数的返回值

```php
$greeting = say_hello();
echo $greeting;
```



函数中添加参数

```php
<?php
function say_hello($name)
{
    return "Hi, $name";
}

$greeting = say_hello('zhang jinglin');
echo $greeting;
```



如果函数有参数，但是调用的时候没有调用参数则会出错。我们也可以给函数参数一个默认值，这样，
调用的时候就不一定要赋值了。

```php
function say_hello($name = 'nobody')
{
    return "Hi, $name";
}
```



我们可以使用`print_r`来输出数组信息，不过显示的结果并不是和直观，所以我们可以写一个工具
函数来输出数组或者变量的信息。

```php
<?php

function pp($value)
{
    echo '<pre>';
    print_r($value);
    echo '</pre>';
}

$arr = ['name' => 'zjl', 'age' => 43, 'ocupation' => 'teacher'];
pp($arr);
```



假设我们有一个这样一个数组，数组的每个元素是一个包含个人信息的数组，也就是这样

```plain
$people = [
    ['name' => 'zjl', 'age' => 43, 'occupation' => 'Teacher'],
    ['name' => 'zhao si', 'age' => 50, 'occupation' => 'Actor'],
    ['name' => 'wang wu', 'age' => 21, 'occupation' => 'Student']
];
```



现在我们希望能有一个函数，能够根据我们提供的关键字来抽取出一个数组，比如抽取出所有的名字
组成的数组，也就是希望

```plain
array_pluck('name', $people); //['zjl', 'zhao si', 'wang wu']
```



我们来实现上面的函数

```php
function array_pluck($toPluck, $arr)
{
    $ret = [];
    foreach($arr as $item) {
        $ret[] = $item[$toPluck];
    }

    return $ret;

}
```



我们现在利用php提供的`array_map`来实现我们上面的函数。

```php
function array_pluck($toPluck, $arr)
{
    return array_map(function($item) {
        return $item[$toPluck];
    }, $arr);
}
```



上面的函数将导致错误，说`$toPluck`是一个没有定义的变量。在php中，变量的作用域仅仅在函数中
，这意味着我们的`array_map`的调用函数中是不存在`$toPluck`这个变量的。为了使用函数外部的变量，
我们需要修改代码

```plain
function array_pluck($toPluck, $arr)
{
    return array_map(function($item) use($toPluck) {
        return $item[$toPluck];
    }, $arr);
}
```



上面代码中的`use($toPluck)`将函数外部的变量带入到了函数中。

## 对象

我们来看一下php的对对象定义

```php
<?php

class Person
{
    public $name;
    public $job;

    //构造函数
    public function __construct($name, $job)
    {
        $this->name = $name;
        $this->job = $job;
    }

    public function introduction()
    {
        return "I'm $this->name, a $this->job";
    }
}

$p = new Person('zjl', 'teacher');
echo $p->introduction();
```



php也提供了一个`stdClass`的类模板，我们可以直接使用。

```php
<?php

$person = new stdClass;
$person->name = 'zjl';
$person->job = 'teacher';

var_dump($person);
echo $person->name;
```




> 在使用`stdClass`中要注意的问题是，不能使用方法，如果必须使用方法的话，就需要自定义类。

还可以使用强制类型转换来将数组转换为`stdClass`。

```php
<?php

$person = [
    'name' => 'zjl',
    'job' => 'teacher'
];

var_dump($person);
var_dump((object)$person);

$o = (object)$person;
echo $person['name'];
echo $o->job;
```





## heredocs

我们来看一下下面的代码：

```php
<?php

$post = [
    'author' => 'zjl',
    'title' => 'My Awesome Post',
    'body' => 'Here is the body',
    'publish-date' => '2016-3-15'
];

// 下面的语句会出错，对数组不能直接这样写
// $email = "<h1>$post['title']";
// 必须修改成带有{}的样式
$email = "<h1>{$post['title']}</h1><p>By: {$post['author']}</p><div>{$post['body']}</div>";

echo $email;
```



如果我们的内容更加复杂的话，$email的赋值语句会让我们崩溃的。我们可以稍微修改一下

```php
$email = "<h1>{$post['title']}</h1>";
$email .= "<p>By: {$post['author']}</p>";
$email .= "<div>{$post['body']}</div>";
```



这样好一些，但仍然不会是一个好的解决方案。我们再进一步，使用php提供的`sprintf()`函数来格式化我们
要输出的信息。

```php
$email = sprintf("<h1>%s</h1><p>By: %s</p><div>%s</div>", $post['title'], $post['author'], $post['body']);
```



有进步，但仍然不是很好。幸好，php提供了`heredocs`的功能。

```php
$email = <<<EOT
<h1>{$post['title']}</h1>
<p>By: {$post['author']}</p>
<div>{$post['body']}</div>
EOT;
```



`<<<EOT`表示heredocs的开始，EOT可以用你自己定义的符号表示，比如`<<<HAHA`，但是结束标志要一致。

php提供了一个`extract()`的函数，它能够将一个关联数组中的数据编程一系列的变量，变量的名字就是key的
名字，值就是原先关联的值。

```plain
extract($post); //生成四个变量，$author,$title,$body,$publish-date

$email = <<<EOT
<h1>$title</h1>
<p>By: $author</p>
<div>$body</div>
EOT;

echo $email;
```
