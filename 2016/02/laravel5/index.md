# Laravel 5 基础


## Laravl 是什么

Laravel是一套简洁、优雅的PHP Web开发框架 (PHP Web Framework) 。在世界（不含中国）PHP框架的占有率超过40%。

## 设置系统

检查composer是否安装，如果没有安装，请参考[Ubuntu PHP 开发环境配置](ubuntu-php/)或者[Cent OS 7 PHP 开发环境配置](cent-os-php-laravel/)

```bash
composer --version
#输出
Composer version 1.0.0-alpha9 2014-12-07 17:15:20
```



建立 Laravel 项目

```bash
cd /var/www/html/
composer create-project laravel/laravel learning-laravel-5 --prefer-dist
#需要等待
```



测试项目

```bash
cd learning-laravel-5/
#x.x.x.x 为虚拟机地址
php -S x.x.x.x:8888 -t public
```



主机浏览器测试

## 路由、控制器和视图简介

- 查看app->Http->routes.php

```php
Route::get('/', 'WelcomeController@index');
```



@是一个界定符，前面是控制器，后面是动作，表示当用户请求url / 的时候，执行控制器 WelcomeController 中的 index 方法

- `app->http->controllers->welcomecontroller.php`

```php
public function index()
{
return view('welcome');
}
```



当前默认返回一个视图，视图的名字叫做 welcome，实际上是 welcome.blade.php，blade是laravel的视图模板。

- 可以查看 resources->views->welcome.blade.php
- 修改welcomecontroller.php

```php
public function index()
{
//    return view('welcome');
return 'hello, laravel';
}
```



在浏览器中测试，得到一个简单的反馈。

我们新建一个路由，在routes.php中增加：

```php
Route::get('/contact', 'WelcomeController@contact');
```



可以新建一个路由，但是现在我们还是直接使用默认的控制器，在 WelcomeController.php 中添加：

```php
public function contact() {
    return 'Contact Me';
}
```



在浏览器终测试新增加的路由。

我们可以返回简单的字符串，也可以返回json或者html文件，所有的视图文件存储在resource->views中。
例如：`return view('welcome')` ,我们不需要考虑路径，也不要添加.blade.php扩展名，框架自动为我们完成。如果在views目录中需要子目录，例如views/forum子目录，只需要`return view('forum/xxx')`，或者跟简单而明确的方式是：`return view('forum.xxx')`。😄

- 我们返回一个页面

```php
public function contact() {
    return view('pages.contact');
}
```




- 在 views 目录下创建 pages目录，然后创建contact.blade.php

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
<h1>Contact</h1>
</body>
</html>
```





## 向视图传送数据

- 我们在Routes.php中新建一个路由

```php
Route::get('about', 'PagesController@about');
```



在浏览器中浏览会获得一个错误，错误信息仅仅是一个提示信息，缺少细节，在生产环境 It’ ok，但是开发阶段我们希望获得详细信息。

- 在项目的根目录找到 .env 文件，修改

```php
APP_DEBUG=true
```



这将显示详细的错误信息，PagesController 不存在。但在生产环境一定要设置为 false

- 我们可以手工新建控制器，但更快的方式是利用 laravel 提供的生成器。在命令行当前项目目录中运行：

```bash
php artisan
```



可以看到laravel提供的功能。

```bash
php artisan make:controller PagesController
```



ok，在 `app->http->controller` 下面生成了 PagesController.php

```php
<?php namespace App\Http\Controllers;

use App\Http\Requests;
use App\Http\Controllers\Controller;

use Illuminate\Http\Request;

class PagesController extends Controller {

	/**
	 * Display a listing of the resource.
	 *
	 * @return Response
	 */
	public function index()
	{
		//
	}

	/**
	 * Show the form for creating a new resource.
	 *
	 * @return Response
	 */
	public function create()
	{
		//
	}

	/**
	 * Store a newly created resource in storage.
	 *
	 * @return Response
	 */
	public function store()
	{
		//
	}

	/**
	 * Display the specified resource.
	 *
	 * @param  int  $id
	 * @return Response
	 */
	public function show($id)
	{
		//
	}

	/**
	 * Show the form for editing the specified resource.
	 *
	 * @param  int  $id
	 * @return Response
	 */
	public function edit($id)
	{
		//
	}

	/**
	 * Update the specified resource in storage.
	 *
	 * @param  int  $id
	 * @return Response
	 */
	public function update($id)
	{
		//
	}

	/**
	 * Remove the specified resource from storage.
	 *
	 * @param  int  $id
	 * @return Response
	 */
	public function destroy($id)
	{
		//
	}

}
```



这样生成的controller包含了全部所需要的RESTful方法，我们可以简化一下。删除生成的PagesController.php，在命令行运行：

```bash
php artisan make:controller PagesController --plain
```



再看一下生成的结果

```php
<?php namespace App\Http\Controllers;

use App\Http\Requests;
use App\Http\Controllers\Controller;

use Illuminate\Http\Request;

class PagesController extends Controller {

	//

}
```



基本上是一个空的controller，所有的方法我们需要自己创建。

如果你想知道到底有什么参数我们可以在命令行执行，你可以运行下面的命令来查看帮助

```bash
php artisan help make:controller
```



ok, 你可以经常使用help命令来帮助你了解这些参数。

- 在PagesController中建立about方法。

```php
public function about() {
       return 'About Page';
   }
```



在浏览器冲查看结果，错误消失，返回简单的信息。

返回视图

我们当然希望返回html文档，修改about方法的返回：

```php
public function about() {
       return view('pages.about');
   }
```



注意：返回的结果是 `pages.about` ，这表示在 `views` 子目录中的 `pages` 子目录中的 `about.balde.php` 文件。让我们创建 `resources\views\pages\about.balde.php` 文件

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
<h1>About</h1>
</body>
</html>
```



That’s it. 运行浏览器查看吧，😄

- 向视图传送数据

修改PagesController.php

```php
public function about() {
       $name = 'Zhang Jinlgin';
       return view('pages.about')->with('name', $name);
   }
```



修改我们的视图文件 about.blade.php

```html
<h1>About <?= $name ?></h1>
```



Bingo，查看结果吧。

我们使用的laravel使用了blade模板，我们可以利用这个好处修改视图：

```html
<h1>About {{ $name }}</h1>
```




看起来更好了，在blade中，{{}}是转义html的语义的，让我来修改一个数据：

```php
$name = '<span style="color: red">Zhang Jinlgin</span>';
```



查看结果，发现所有的html元素都被转义了。但是如果不需要转义html，可以使用 {!! !!}，修改视图：

```html
<h1>About {!! $name !!}</h1>
```



再看结果，👌

- 我们不仅仅可以向视图传送一个数据，同样我们可以传送Array

```php
public function about()
{
    return view('pages.about')->with([
        'first' => 'Zhang',
        'last' => 'Jinglin'
    ]);
}
```






```html
<h1>About {{ $first }} {{ $last }}</h1>
```



一种简洁的方式是这样：

```php
public function about()
{
    $data = [];
    $data['first'] = 'Zhang';
    $data['last'] = 'Jinglin';
    
    return view('pages.about', $data);
}
```



结果相同，更简单的是这样：

```php
public function about()
{
    $first = 'Zhang';
    $last = 'Jinglin';
    return view('pages.about', compact('first', 'last'));
}
```



compact 把参数变成数组，extract相反。可以查看php手册了解 compact ，顺道了解一下 extract。

## Blade 简介

在多个页面中我们可能包含相同的内容，像是文件头，链接的css或者js等。我们可以利用布局文件完成这个功能。

让我们新建一个布局文件，例如 `views/layout.blade.php`

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <link rel="stylesheet" href="http://cdn.bootcss.com/bootstrap/3.3.4/css/bootstrap.min.css">
</head>
<body>
    <div class="container">
        @yield('content')
    </div>
</body>
</html>
```



我们创建了不解的结构，引入了bootstrap，注意 `@yield` 是blade的布局占位符，未来我们的页面内容将填充到这里，修改 `about.blade.php`

```html
@extends('layout')

@section('content')
    
<h1>About {{ $first }} {{ $last }}</h1>

@stop
```



上面的代码表示我们使用布局文件 `layout.blade.php`， 然后在 `content` 段中添加内容。

在 `routes.php` 中添加：

```php
Route::get('about', 'PagesController@about');
Route::get('contact', 'PagesController@contact');
```



在 `PagesController.php` 中添加：

```php
public function contact() {
    return view('pages.contact');
}
```



新建视图 `pages/contact.blade.php`

```html
@extends('layout')

@section('content')
    <h1>Contact Me!</h1>
@stop
```



Check it out!

在布局文件中我们可以添加多个 `@yield` , 比如在 `layout.blade.php` 中添加 `@yield('footer')` ：

````html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <link rel="stylesheet" href="http://cdn.bootcss.com/bootstrap/3.3.4/css/bootstrap.min.css">
</head>
<body>
    <div class="container">
        @yield('content')
    </div>

    @yield('footer')
</body>
</html>
``` 
	
比如 `contact.blade.php` 中有一段脚本，就可以放在这个段中。

```html
@extends('layout')

@section('content')
    <h1>Contact Me!</h1>
@stop

@section('footer')
    <script>
        alert('Contact from scritp')
    </script>
@stop
````



访问contact会有对话框，而about仍然是普通显示

- 使用@if进行判断

```html
@extends('layout')

@section('content')
    @if ($first = 'Zhang')
        <h1>Hello, Zhang</h1>
    @else
        <h1>Hello, nobody</h1>
    @endif
@stop
```



也可以视同 `@unless` 等同于 `if !`， 还有 `@foreach` 等。

```php
public function about()
{
    $people = [
        'zhang san',
        'li si',
        'wang wu'
    ];
    return view('pages.about', compact('people'));
}
```






```html
@extends('layout')

@section('content')
    <h1>Person:</h1>
    <ul>
        @foreach($people as $person)
            <li>{{ $person }}</li>
        @endforeach
    </ul>
@stop
```



有一种情况，数据可能来自数据库，集合可能是空，像是这样：

```php
$people = [];
```



处理这种情况，请添加 `@if` 处理

```html
@extends('layout')

@section('content')
    @if (count($people))
        <h1>Person:</h1>
        <ul>
            @foreach($people as $person)
                <li>{{ $person }}</li>
            @endforeach
        </ul>
    @endif

    <h2>Other info</h2>
@stop
```



That’s better.

## 环境与配置

`.env` 文件是配置文件，包括数据库配置信息，查看 `config->database.php` ，`connections` 里面包含了所有数据库的配置，可以在 `default` 中选择要使用的数据库。在数据库配置中，有关 `env('DB_HOST', 'localhost')` 就是读取 `.env` 配置文件的信息。第二个参数是缺省参数。

- 我们使用 `mysql` 数据库，修改 `.env`:

```php
DB_HOST=localhost
DB_DATABASE=laravel
DB_USERNAME=root
DB_PASSWORD=
```




- 在mysql中创建 `laravel`数据库

```bash
mysql -u root 

CREATE DATABASE laravel
```




- 查看 `mysql` 配置：

```php
  'mysql' => [
	'driver'    => 'mysql',
	'host'      => env('DB_HOST', 'localhost'),
	'database'  => env('DB_DATABASE', 'forge'),
	'username'  => env('DB_USERNAME', 'forge'),
	'password'  => env('DB_PASSWORD', ''),
	'charset'   => 'utf8',
	'collation' => 'utf8_unicode_ci',
	'prefix'    => '',
	'strict'    => false,
],
```




- 在 `config` 子目录中，包含了全部的配置文件，查看一下。

## 数据库迁移

database migrations 是laravel最强大的功能之一。数据库迁移可以理解为数据库的版本控制器。

在 `database/migrations` 目录中包含两个迁移文件，一个建立用户表，一个用于用户密码重置。

在迁移文件中，`up` 方法用于创建数据表，`down`方法用于回滚，也就是删除数据表。

- 执行数据库迁移

```bash
php artisan migrate
#输出
Migration table created successfully.
Migrated: 2014_10_12_000000_create_users_table
Migrated: 2014_10_12_100000_create_password_resets_table
```



查看mysql数据库，可以看到产生了三张表。 `migratoins` 表是迁移记录表，`users` 和 `pasword_resets`。

- 如果设计有问题，执行数据库回滚

```bash
php artisan migrate:rollback
#输出
Rolled back: 2014_10_12_100000_create_password_resets_table
Rolled back: 2014_10_12_000000_create_users_table
```



再次查看mysql数据库，就剩下 `migrations` 表了， `users password_resets` 被删除了。

修改迁移文件，再次执行迁移。

- 新建迁移

```bash
php artisan make:migration create_article_table --create='articles'
#输出
Created Migration: 2015_03_28_050138_create_article_table
```



在 `database/migrations` 下生成了新的文件。

```php
<?php

use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreateArticleTable extends Migration {

	/**
	 * Run the migrations.
	 *
	 * @return void
	 */
	public function up()
	{
		Schema::create('articles', function(Blueprint $table)
		{
			$table->increments('id');
			$table->timestamps();
		});
	}

	/**
	 * Reverse the migrations.
	 *
	 * @return void
	 */
	public function down()
	{
		Schema::drop('articles');
	}

}
```



自动添加了 `id`列，自动增长，`timestamps()` 会自动产生 `created_at` 和 `updated_at` 两个时间列。我们添加一些字段：

```php
public function up()
{
	Schema::create('articles', function(Blueprint $table)
	{
		$table->increments('id');
           $table->string('title');
           $table->text('body');
           $table->timestamp('published_at');
		$table->timestamps();
	});
}
```



执行迁移：

```bash
php artisan migrate
```



现在有了新的数据表了。😄

假设我们需要添加一个新的字段，你可以回滚，然后修改迁移文件，再次执行迁移，或者可以直接新建一个迁移文件

```bash
php artisan make:migration add_excerpt_to_articels_table
```



查看新产生的迁移文件

```php
<?php

use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class AddExcerptToArticelsTable extends Migration {

	/**
	 * Run the migrations.
	 *
	 * @return void
	 */
	public function up()
	{
		//
	}

	/**
	 * Reverse the migrations.
	 *
	 * @return void
	 */
	public function down()
	{
		//
	}

}
```



只有空的 up 和 down 方法。我们可以手工添加代码，或者我们让laravel为我们生成基础代码。删除这个文件，重新生成迁移文件，注意添加参数：

```bash
php artisan make:migration add_excerpt_to_articels_table --table='articles'
```



现在，`up` 方法里面有了初始代码。

```php
public function up()
{
	Schema::table('articles', function(Blueprint $table)
	{
		//
	});
}
```



添加实际的数据修改代码：

```php
public function up()
{
	Schema::table('articles', function(Blueprint $table)
	{
		$table->text('excerpt')->nullable();
	});
}

public function down()
{
	Schema::table('articles', function(Blueprint $table)
	{
		$table->dropColumn('excerpt');
	});
}
```



`nullable()` 表示字段也可以为空。

再次执行迁移并检查数据库。

如果我们为了好玩，执行回滚

```bash
php artisan migrate:rollback
```



`excerpt` 列没有了。

## Eloquent （laravel 的ORM）

- 我们来生成第一个模型

```bash
php artisan make:model Article
#输出
Model created successfully.
Created Migration: 2015_03_28_062517_create_articles_table
```



查看一下生成的文件 `app/Article.php`

```php
<?php namespace App;

use Illuminate\Database\Eloquent\Model;

class Article extends Model {

	//

}
```



没什么特别的，除了继承自 Model 以外，但是具有强大的功能，这些都封装在laravel的Model中。模型自动具有了 `save() update() findXXX()` 等强大的功能。

- tinker 是 laravel提供的命令行工具，可以和项目进行交互。

```bash
php artisan tinker

#以下是在tinker中的交互输入
Psy Shell v0.4.1 (PHP 5.4.16 — cli) by Justin Hileman
>>> $name = 'zhang jinglin';
=> "zhang jinglin"

>>> $name
=> "zhang jinglin"

>>> $article = new App\Article;
=> <App\Article #000000005c4b7ee400000000ab91a676> {}

>>> $article->title = 'My First Article';
=> "My First Article"

>>> $article->body = 'Some content...';
=> "Some content..."

>>> $article->published_at = Carbon\Carbon::now();
=> <Carbon\Carbon #000000005c4b7ee600000000ab91dcb6> {
       date: "2015-03-28 06:37:22",
       timezone_type: 3,
       timezone: "UTC"
   }

>>> $article;
=> <App\Article #000000005c4b7ee400000000ab91a676> {
       title: "My First Article",
       body: "Some content...",
       published_at: <Carbon\Carbon #000000005c4b7ee600000000ab91dcb6> {
           date: "2015-03-28 06:37:22",
           timezone_type: 3,
           timezone: "UTC"
       }
   }

>>> $article->toArray();
=> [
       "title"        => "My First Article",
       "body"         => "Some content...",
       "published_at" => <Carbon\Carbon #000000005c4b7ee600000000ab91dcb6> {
           date: "2015-03-28 06:37:22",
           timezone_type: 3,
           timezone: "UTC"
       }
   ]

>>> $article->save();
=> true

#查看数据结果，添加了一条记录

>>> App\Article::all()->toArray();
=> [
       [
           "id"           => "1",
           "title"        => "My First Article",
           "body"         => "Some content...",
           "published_at" => "2015-03-28 06:37:22",
           "created_at"   => "2015-03-28 06:38:53",
           "updated_at"   => "2015-03-28 06:38:53"
       ]
   ]

>>> $article->title = 'My First Update Title';
=> "My First Update Title"

>>> $article->save();
=> true

>>> App\Article::all()->toArray();
=> [
       [
           "id"           => "1",
           "title"        => "My First Update Title",
           "body"         => "Some content...",
           "published_at" => "2015-03-28 06:37:22",
           "created_at"   => "2015-03-28 06:38:53",
           "updated_at"   => "2015-03-28 06:42:03"
       ]
   ]
   
>>> $article = App\Article::find(1);
=> <App\Article #000000005c4b7e1600000000ab91a676> {
       id: "1",
       title: "My First Update Title",
       body: "Some content...",
       published_at: "2015-03-28 06:37:22",
       created_at: "2015-03-28 06:38:53",
       updated_at: "2015-03-28 06:42:03"
   }

>>> $article = App\Article::where('body', 'Some content...')->get();
=> <Illuminate\Database\Eloquent\Collection #000000005c4b7e1800000000ab91a676> [
       <App\Article #000000005c4b7e1b00000000ab91a676> {
           id: "1",
           title: "My First Update Title",
           body: "Some content...",
           published_at: "2015-03-28 06:37:22",
           created_at: "2015-03-28 06:38:53",
           updated_at: "2015-03-28 06:42:03"
       }
   ]

>>> $article = App\Article::where('body', 'Some content...')->first();
=> <App\Article #000000005c4b7e1900000000ab91a676> {
       id: "1",
       title: "My First Update Title",
       body: "Some content...",
       published_at: "2015-03-28 06:37:22",
       created_at: "2015-03-28 06:38:53",
       updated_at: "2015-03-28 06:42:03"
   }
>>> 

>>> $article = App\Article::create(['title' => 'New Article', 'body' => 'New body', 'published_at' => Carbon\Carbon::now()]);
Illuminate\Database\Eloquent\MassAssignmentException with message 'title'
```





### MassAssignmentException，laravel保护我们不能直接插入记录。比如，在一些特殊情况下我们需要直接利用表单的信息填充数据库记录，但是如果我们并没有在表单中添加密码字段，而黑客产生了密码字段连同我们的其他字段一起送回服务器，这将产生修改密码的危险，所以我们必须明确的告诉laravel我们的模型那些字段是可以直接填充的。

修改我们的模型文件 `Article.php`

```php
<?php namespace App;

use Illuminate\Database\Eloquent\Model;

class Article extends Model {

	protected $fillable = [
        'title',
        'body',
        'published_at'
    ];

}
```





### 表示，title, body, published_at 是可以直接填充的。

退出 tinker，重新进入

```bash

>>> $article = App\Article::create(['title' => 'New Article', 'body' => 'New body', 'published_at' => Carbon\Carbon::now()]);
=> <App\Article #000000005051b2c7000000007ec432dd> {
       title: "New Article",
       body: "New body",
       published_at: <Carbon\Carbon #000000005051b2c6000000007ec4081d> {
           date: "2015-03-28 06:55:19",
           timezone_type: 3,
           timezone: "UTC"
       },
       updated_at: "2015-03-28 06:55:19",
       created_at: "2015-03-28 06:55:19",
       id: 2
   }
   
# It's ok

>>> App\Article::all()->toArray();
=> [
       [
           "id"           => "1",
           "title"        => "My First Update Title",
           "body"         => "Some content...",
           "published_at" => "2015-03-28 06:37:22",
           "created_at"   => "2015-03-28 06:38:53",
           "updated_at"   => "2015-03-28 06:42:03"
       ],
       [
           "id"           => "2",
           "title"        => "New Article",
           "body"         => "New body",
           "published_at" => "2015-03-28 06:55:19",
           "created_at"   => "2015-03-28 06:55:19",
           "updated_at"   => "2015-03-28 06:55:19"
       ]
   ]

>>> $article = App\Article::find(2);
=> <App\Article #000000005051b22b000000007ec432dd> {
       id: "2",
       title: "New Article",
       body: "New body",
       published_at: "2015-03-28 06:55:19",
       created_at: "2015-03-28 06:55:19",
       updated_at: "2015-03-28 06:55:19"
   }

>>> $article->update(['body' => 'New Updaet Body']);
=> true

#update自动调用save()
```





## 模型、控制器、视图基础流程

- 添加路由

```php
Route::get('artiles', 'ArticlesController@index');
```




- 创建控制器

```bash
php artisan make:controller ArticlesController --plain
```




- 修改控制器

```php
<?php namespace App\Http\Controllers;

use App\Article;
use App\Http\Requests;
use App\Http\Controllers\Controller;

use Illuminate\Http\Request;

class ArticlesController extends Controller {

	public function index() {
        $articles = Article::all();

        return $articles;
    }

}
```



可以在浏览器中看到返回的 JSON 结果，cool！

修改控制器，返回视图

```php
public function index() {
       $articles = Article::all();

       return view('articles.index', compact('articles'));
   }
```



创建视图

```html
@extends('layout')

@section('content')
    <h1>Articles</h1>

    @foreach($articles as $article)
        <article>
            <h2>{{$article->title}}</h2>

            <div class="body">{{$article->body}}</div>
        </article>
    @endforeach
@stop
```



浏览结果，COOL！！！！

- 显示单个文章

添加显示详细信息的路由

```php
Route::get('articles/{id}', 'ArticlesController@show');
```



其中，`{id}` 是参数，表示要显示的文章的 id，修改控制器：

```php
public function show($id) {
    $article = Article::find($id);

    //若果找不到文章
    if (is_null($article))
    {
        //生产环境 APP_DEBUG=false
        abort(404);
    }
    return view('articles.show', compact('article'));
}
```



laravel 提供了更加方便的功能，修改控制器：

```php
public function show($id) {
    $article = Article::findOrFail($id);

    return view('articles.show', compact('article'));
}
```



It’s cool.

新建视图

```html
@extends('layout')

@section('content')
    <h1>{{$article->title}}</h1>

    <article>
        {{$article->body}}
    </article>
@stop
```



在浏览器中尝试访问：`/articles/1 /articles/2`

修改index视图

```html
@extends('layout')

@section('content')
    <h1>Articles</h1>
    <hr/>
    @foreach($articles as $article)
        <article>
            <h2>
                {{--这种方式可以--}}
                <a href="/articles/{{$article->id}}">{{$article->title}}</a>
                {{--这种方式更加灵活，不限制路径--}}<br>
                <a href="{{action('ArticlesController@show', [$article->id])}}">{{$article->title}}</a>
                {{--还可以使用--}}<br>
                <a href="{{url('/articles', $article->id)}}">{{$article->title}}</a>
            </h2>

            <div class="body">{{$article->body}}</div>
        </article>
    @endforeach
@stop
```





## 表单

首先让我们修改路由，能够增加一个文章的发布。

```php
Route::get('articles/create', 'ArticlesController@create');
```



然后修改控制器

```php
public function create() {
    return view('articles.create');
}
```



我们返回一个视图，新建这个视图。我们当然可以直接使用HTML建立表单，但我们有功能更好的办法。我们使用一个开源库，Jeffrey Way 开发的[illuminate\html](http://github.com/illuminate/html)。安装依赖库：

```bash
composer require illuminate/html
```



laravel的库需要注册到laravel中才能使用。在 `config/app.php` 中，我们可以看到 laravel 提供的 `provider` 字段，这里描述了laravel的库功能。在`Laravel Framewirk Service Providers...` 最后添加我们新增的 `HtmlProvider`

```php
'Illuminate\Html\HtmlServiceProvider',
```



我们不希望使用 `Illuminate\Html\FromFacade` 这么长的名字来引入，我们需要简短的名字。在当前的 `app.php` 中找到 aliases 段，在最后添加别名。

```php
'Form'      => 'Illuminate\Html\FormFacade',
'Html'      => 'Illuminate\Html\HtmlFacade',
```



OK，现在我们来创建视图，`views/articles/create.blade.php`

```html
@extends('layout')

@section('content')
    <h1>Write a New Article</h1>

    <hr/>

    {{--使用我们添加的 illuminate\html 开源库--}}
    {!! Form::open() !!}

    {!! Form::close() !!}

@stop
```



访问 `/articles/create` 看到了错误，Why? 让我们测试一下，到底是哪里出了问题。在控制器中做出下面的修改：

```php
public function show($id) {
    dd('show');
    $article = Article::findOrFail($id);
    return view('articles.show', compact('article'));
}
```



没错，你没看错，就是在 `show` 方法中添加 `dd()` 方法，这个方法简单的输出一个信息然后死掉。我们再来访问 `/articles/create` ，你看到了什么，你看到输出了 `show` 。

为什么我们访问 `create` 结果路由给了我们 `show` ? 我们来查看一下路由，到底发生了什么。

```php
Route::get('articles', 'ArticlesController@index');
Route::get('articles/{id}', 'ArticlesController@show');
Route::get('articles/create', 'ArticlesController@create');
```



上面是我们的路由，注意到 `articles/{id}` 意味着这是一个通配符，所有在 `articles/` 后面的东西都会匹配，你知道了么？我们的 `/articles/create` 也被他匹配了。OMG！

解决方案就是调整顺序：

```php
Route::get('articles', 'ArticlesController@index');
Route::get('articles/create', 'ArticlesController@create');
Route::get('articles/{id}', 'ArticlesController@show');
```



也就是从特殊到普通，以后的路由设置中要时刻注意这个问题。现在我们在访问 `articles/create` 一切OK了。

在浏览器中查看一下源代码，你会发现不仅生成了 `method` 和 `action` 同时生成了一个隐藏的 `_token` 字段作为服务器对窗体的验证，避免黑客的伪造攻击。

让我们修改我们的视图，添加字段：

```html
@extends('layout')

@section('content')
    <h1>Write a New Article</h1>

    <hr/>

    {{--使用我们添加的 illuminate\html 开源库--}}
    {!! Form::open() !!}
        <div class="form-group">
            {!! Form::label('title', 'Title:') !!}
            {!! Form::text('title', null, ['class' => 'form-control']) !!}
        </div>

        <div class="form-group">
            {!! Form::label('body', 'Body:') !!}
            {!! Form::textarea('body', null, ['class' => 'form-control']) !!}
        </div>

        <div class="form-group">
            {!! Form::submit('Add Article', ['class' => 'btn btn-primary form-control']) !!}
        </div>

    {!! Form::close() !!}

@stop
```



当表单提交的时候，实际上是使用 `post` 方法提交到 `articles/create` 上的，但根据RESTful的习惯，我们希望能够 `post` 到 `/articles` 上，我们来修改视图的表单方法，设定提交的路径。

```html
{!! Form::open(['url' => 'articles']) !!}
```



然后我们在路由中处理表单提交事件。

```php
Route::post('/articles', 'ArticlesController@store');
```



我们来处理控制器

```php

//注意：将下面的 use 语句删除，我们使用 facade 接口中的 Request
//use App\Http\Requests\Request;

//引入下面的命名空间中的 Request
use Illuminate\Support\Facades\Request;

    public function store() {
        //使用 Illuminate\Html\Request 来返回全部的表单输入字段
        $input = Request::all();

        //我们直接返回$input，来看一下
        return $input;
    }
```



我们可以直接看到输入表单的json结果。如果只需要 `title` 字段的值，则可以使用 `Request::get('titel')` 。

如何添加到数据库中呢？借助模型，我们可以直接采用下面的方法，

```php
Article::create($input);
```



###就这么简单，就是这么任性

###如果没有忘记 Mass Assignment，在我们的模型中我们定义了 `$fillable` 数组，来定义那些字段可以直接在 `create` 的时候直接填充。

修改控制器，添加到模型中，并存储到数据库。

```php
public function store() {
    $input = Request::all();
    Article::create($input);

    return redirect('articles');
}
```



添加一条记录试试，非常棒。但别忘了。我们还有一个字段叫做 `published_at` ，让我们来处理它。

```php
public function store() {
    $input = Request::all();
    $input['published_at'] = Carbon::now();

    Article::create($input);
    
    return redirect('articles');
}
```



添加新纪录在测试一下。

还有一个问题，新添加的应该显示在最前面，我们来修改以下控制器。

```php
public function index() {
       //倒序获取文章
       //可以这样
       //$articles = Article::orderBy('published_at', 'desc')->get();
       //简单方式，当然还有 oldest()
       $articles = Article::latest('published_at')->get();

       return view('articles.index', compact('articles'));
   }
```





## 日期，Mutator 和 Scope

在我们前面的解决方案中，直接给 published_at 赋值为当前日期实际上是一个临时解决方案，我们需要设定发布日期，可能是未来2天后才发布，让我们修改这个问题。

首先修改控制器：

```php
public function store() {
    Article::create(Request::all());
    return redirect('articles');
}
```



然后修改视图，添加发布日期字段

```html
@extends('layout')

@section('content')
    <h1>Write a New Article</h1>

    <hr/>

    {{--使用我们添加的 illuminate\html 开源库--}}
    {!! Form::open(['url' => 'articles']) !!}
        <div class="form-group">
            {!! Form::label('title', 'Title:') !!}
            {!! Form::text('title', null, ['class' => 'form-control']) !!}
        </div>

        <div class="form-group">
            {!! Form::label('body', 'Body:') !!}
            {!! Form::textarea('body', null, ['class' => 'form-control']) !!}
        </div>

        <div class="form-group">
            {!! Form::label('published_at', 'Publish On:') !!}
            {!! Form::input('date', 'published_at', date('Y-m-d'), ['class' => 'form-control']) !!}
        </div>

        <div class="form-group">
            {!! Form::submit('Add Article', ['class' => 'btn btn-primary form-control']) !!}
        </div>

    {!! Form::close() !!}

@stop
```



ok，让我们添加一个新的文章，并且把日期设置为未来的某一天，但是文章直接显示在最开始了，这不是我们需要的。我们需要到了那天才显示出来。当然，我们需要更具体一点，比如在早上 8：00 显示，而不是0点显示。我们可以添加一个mutator（也就是其他语言的属性设置器），修改我们的model

```php
<?php namespace App;

use DateTime;
use Illuminate\Database\Eloquent\Model;

class Article extends Model {

	protected $fillable = [
        'title',
        'body',
        'published_at'
    ];

    //属性设置其要遵守格式约定
    // set属性Attribute
    public function setPublishedAtAttribute($date) {
        $this->attributes['published_at'] = Carbon::createFromFormat('Y-m-d', $date)->hour(8)->minute(0)->second(0);
    }

}
```



添加一个新的纪录，查看数据库，我们已经将时间设置正确了，但是我们的首页仍然显示未来的才发布的文章，我们修改它。

```php
public function index() {
       //$articles = Article::latest('published_at')->get();
       $articles = Article::latest('published_at')->where('published_at', '<=', Carbon::now())->get();

       return view('articles.index', compact('articles'));
   }
```



上面的解决方法可以工作，但是查询语句太长了。我们可以使用 Laravel 提供的 scope ，来简化我们的工作。所谓scope可以理解为是查询过程中使用的中间查询结果，比如我们定义一个published scope，他可以返回所有当前已经发布的文章，让我们修改模型。

```php
//设置scope，遵守命名规则
public function scopePublished($query) {
    $query->where('published_at', '<=', Carbon::now());
}
```



修改控制器使用 scope

```php
public function index() {
       //$articles = Article::latest('published_at')->get();
       //$articles = Article::latest('published_at')->where('published_at', '<=', Carbon::now())->get();
       $articles = Article::latest('published_at')->published()->get();

       return view('articles.index', compact('articles'));
   }
```



结果相同，但在复杂的查询中我们可以使用scope来分解我们的任务，或者复用查询。

我们来增加一个新的查询，查询所有还没有发布的文章。在模型中添加scope

```php
public function scopeUnpublished($query) {
    $query->where('published_at', '>', Carbon::now());
}
```



修改控制器使用unpulished

```php
public function index() {
       //$articles = Article::latest('published_at')->get();
       //$articles = Article::latest('published_at')->where('published_at', '<=', Carbon::now())->get();
       //$articles = Article::latest('published_at')->published()->get();
       $articles = Article::latest('published_at')->Unpublished()->get();

       return view('articles.index', compact('articles'));
   }
```



one more thing! 如果我们在 `show` 方法中使用 `dd($article->published_at)` 查看的结果，与 `dd($article->created_at);` 结果不一样，前者我们使我们自己的字段，后者是在 `CreateArticleTable` 中通过 `$table->timestamp()` 自动生成的。自动生成的字段显示出来是 `Carbon`类型，而我们的是字符串。使用 Crabon类型有很多的好处，例如你可以输出 `dd($article->created_at->diffForHumans());` ，这种 `1 hour ago` 结果，但我们的`published_at` 不可以。怎么修改？修改模型，告诉laravel，`published_at` 是日期即可。

```php
protected $dates = ['published_at'];
```



再次使用 `dd($article->published_at->diffForHumans());` ，结果显示为 `3 days from now`，Bingo！

## 表单验证

在建立一个文章的时候，如果你什么都不输入直接提交，ok，你获得了一个空的文章，没有任何错误提示，这是不对的。在命令行下运行 `php artisan` 可以看到一个选项 `make:request`，新建一个form request类。在命令行执行

```bash
php artisan make:request CreateArticleRequest
```



生成的文件在 `app/http/requests` 目录下。在文件中我们可以看到两个方法：

```plain
public function authorize()
{
	return false;
}

public function rules()
{
	return [
		//
	];
}
```



`authorize` 表示用户在提交表单的时候是否需要是认证用户，我们不需要认证，返回 true。`rules`是我们的规则方法。让我们修改这个方法：

```plain
public function authorize()
{
	//修改为 true，表示不需要认证，或者是通过认证
	return true;
}

public function rules()
{
	return [
		'title' => 'required|min:3',
       'body' => 'required',
       'published_at' => 'required|date'
	];
}
```



其他的约束可以插看 laravel 的文档。上面的约束表示 `title` 是必须输入的，最少3个字符，`body` 是必须的，`published_at` 是必须的而且是日期。

在视图中，我们总是可以访问 `$errors` 变量来判断我们是否有错误，修改视图

```html

@if ($errors->any())
    <ul class="alert alert-danger">
        @foreach($errors->all() as $error)
            <li>{{ $error }}</li>
        @endforeach
    </ul>
@endif

{{--使用我们添加的 illuminate\html 开源库--}}
{!! Form::open(['url' => 'articles']) !!}
```



修改控制器，引入我们的 Request 类。

```php
public function store(Requests\CreateArticleRequest $request) {
    Article::create($request->all());

    return redirect('articles');
}
```



再次提交表单，什么都不填，可以看到错误信息。

### 修改提示信息变为中文

显示的是英文的错误信息，实际上laravel考虑到了国际化的问题，首先修改 `config/app.php` ，

```php
'locale' => 'zh',
```



将 locale 语言设置为中文，然后再 `resources/lang` 下面新建文件夹 `zh`， 拷贝 `resources/lang/en/validation.php` 文件到 `zh` 目录下，修改：

```plain
"min"                  => [
	"numeric" => "The :attribute must be at least :min.",
	"file"    => "The :attribute must be at least :min kilobytes.",
	"string"  => ":attribute 至少要包含 :min 字符。",
	"array"   => "The :attribute must have at least :min items.",
],

"required"             => ":attribute 必须填写。",
```



其他的可以自行翻译。再次提交空表单，错误信息为中文了。而且 `min:3` 的判断也为最少3个中文。

–

laravel 也在控制器中集成了 `validate` 方法，换句话说，我们不一定要生成 request 类，这些工作我们可以直接在控制器中完成。

修改控制器：

```php
//注意 Request 的命名空间，不要弄错了
  public function store(\Illuminate\Http\Request $request) {

      $this->validate($request, [
          'title' => 'required|min:3',
          'body' => 'required',
          'published_at' => 'required|date'
      ]);

      Article::create($request->all());

      return redirect('articles');
  }
```



结果相同，这样可以更快速的完成简单的验证。

## 子视图和表单复用

我们需要处理编辑文章的问题。当然我们可以手工添加新的路由，就像这样：

```php
Route::get('/articles/{id}/edit', 'ArticleController@edit');
```



让我们在命令行下使用 `artisan` 的 `route:list` 来查看我们当前的路由：

```bash
php artisan route:list
```



在符合 RESTful 的情况下，可能直接使用 laravel 的 `resource` 路由是一种好的选择，然我们将所有的路由都去掉，只添加唯一的一个：

```php
Route::resource('articles', 'ArticlesController');
```



再次使用 `php artisan route:list` 查看路由，哇，一堆的符合我们期望的路由产生了。每一项都仔细查看一下。

现在在控制器中添加方法：

```php

public function edit($id) {
    $article = Article::findOrFail($id);

    return view('articles.edit', compact('article'));
}
```



现在创建视图

```html
@extends('layout')

@section('content')
    <h1>Edit: {!! $article->title !!} </h1>

    <hr/>

    ...
```



好吧，我承认这些代码都是从 `create.blade.php` 中拷贝出来的，修改了一下，问题是我们需要重复吗？后面我们会处理这个问题，现在来看一下表单的提交问题。在路由中 `php artisan route:list` ，再看一遍，修改使用了 `PATCH`  方法，我们来修改视图：

```html
{!! Form::open(['method' => 'PATCH', 'url' => 'articles/' . $article->id]) !!}
```



在浏览器中访问 `/articles/1/edit` ，查看一下源代码，发现laravel自动生成了 `_method=PATCH` 的隐藏字段。

一问题是，我们编辑文章，但是文章的信息并没有显示出来，我们修改一下视图：

```plain
{!! Form::model($article, ['method' => 'PATCH', 'url' => 'articles/' . $article->id]) !!}
```



OK，everything’s ok，除了 `published_on` 字段仍然设置为当前日期，后面我们来处理。

现在在控制器中添加方法：

```php
public function update($id, \Illuminate\Http\Request $request) {
    $article = Article::findOrFail($id);
    $article->update($request->all());

    return redirect('articles');
}
```



我们在修改的过程中也需要验证，让我们复用我们的 Request 类，将 `CreateArticleRequest` 更名为更通用的 `ArticleRequest`，别忘了修改 `store` 方法中的参数。

```php
public function update($id, Requests\ArticleRequest $request) {
    $article = Article::findOrFail($id);
    $article->update($request->all());

    return redirect('articles');
}
```



现在剩下的问题是我们的新建和编辑使用了大部分相同的代码，比如显示错误，但他们存在两份，我们来修改这个问题。

我们直接在 `views/articles` 下面新建文件 `list.blade.php`，并把错误处理代码从 `create.blade.php` 中拷贝出来：

```plain
@if ($errors->any())
    <ul class="alert alert-danger">
        @foreach($errors->all() as $error)
            <li>{{ $error }}</li>
        @endforeach
    </ul>
@endif
```



在 `create.blade.php` 只需用下面语句替换错误处理代码即可：

```plain
@include('articles.list')
```



让我们再来处理表单代码，表单代码中除了 `form` 不大一样和提交按钮有区别，其他都差不多。我们创建一个视图 `articles/form_partial.blade.php`，将代码拷贝出来

```html
<div class="form-group">
    {!! Form::label('title', 'Title:') !!}
    {!! Form::text('title', null, ['class' => 'form-control']) !!}
</div>

<div class="form-group">
    {!! Form::label('body', 'Body:') !!}
    {!! Form::textarea('body', null, ['class' => 'form-control']) !!}
</div>

<div class="form-group">
    {!! Form::label('published_at', 'Publish On:') !!}
    {!! Form::input('date', 'published_at', date('Y-m-d'), ['class' => 'form-control']) !!}
</div>

<div class="form-group">
    {{--这里要设置变量，依据是编辑还是修改来改变，当然也可以不放置在partial中--}}
    {!! Form::submit($submitButtonText, ['class' => 'btn btn-primary form-control']) !!}
</div>
```



修改 `create.blade.php`

```php
@extends('layout')

@section('content')
    <h1>Write a New Article</h1>

    <hr/>

    @include('articles.list')

    {{--使用我们添加的 illuminate\html 开源库--}}
    {!! Form::open(['url' => 'articles']) !!}
        @include('articles.form_partial', ['submitButtonText' => 'Add Article'])
    {!! Form::close() !!}

@stop
```



修改 `edit.blade.php`

```php
@extends('layout')

@section('content')
    <h1>Edit: {!! $article->title !!} </h1>

    <hr/>

    @include('articles.list')
    {{--使用我们添加的 illuminate\html 开源库--}}
    {!! Form::model($article, ['method' => 'PATCH', 'url' => 'articles/' . $article->id]) !!}
    @include('articles.form_partial', ['submitButtonText' => 'Update Article'])

    {!! Form::close() !!}

@stop
```





## Eloquent 关系

一个用户可能有多个文章，一个文章是某个用户书写的，这就是关系。同样文章中可能包含多个 TAG，而一个 TAG 可能关联多个文章。

在项目中，我们已经有了 `User.php`，也就是用户模型，查看一下，相当简单。我们希望直接使用 `$user->articles()` 的形式获取全部文章，让我们修改 user 模型：

```php
public function articles() {
    return $this->hasMany('App\Article');
}
```



但是我们仅仅完成了关系中的一端，让我们来处理另一端。我们希望的形式是 `$article->user()` ，让我们修改 article 模型。

```php
public function user() {
    return $this->belongsTo('App\User');
}
```



在我们的数据库中，文章模型没有用户的外键，我们需要设置，修改 `create_article_table.php`

```php

Schema::create('articles', function(Blueprint $table)
{
	$table->increments('id');
          //指定外键列
          $table->integer('user_id')->unsigned();
          $table->string('title');
          $table->text('body');
          $table->timestamp('published_at');
	$table->timestamps();

          //生成外键
          $table->foreign('user_id')
              ->references('id')
              ->on('users')
              ->onDelete('cascade');
});
```



因为我们只是在开发阶段，还没有上线运行，我们可以直接修改数据库迁移文件，回滚然后迁移即可，但是如果上线运行的话，应该新建一个迁移。

```bash
php artisan migrate:refresh

#输出信息
Rolled back: 2015_03_28_050138_create_article_table
Rolled back: 2014_10_12_100000_create_password_resets_table
Rolled back: 2014_10_12_000000_create_users_table
Nothing to rollback.
Migrated: 2014_10_12_000000_create_users_table
Migrated: 2014_10_12_100000_create_password_resets_table
Migrated: 2015_03_28_050138_create_article_table
Migrated: 2015_03_28_051200_add_excerpt_to_articels_table
```



现在让我们使用 `tinker` 来创建一个用户。

```bash
php artisan tinker
Psy Shell v0.4.1 (PHP 5.4.16 — cli) by Justin Hileman

#下面是执行过程
>>> $user = new App\User;
=> <App\User #000000007f1ad61a000000006497cc4f> {}
>>> $user->name = 'zhang jinglin';
=> "zhang jinglin"
>>> $user->email = 'zjl@example.com';
=> "zjl@example.com"
>>> $user->password = bcrypt('pass');
=> "$2y$10$Nbl2b9wqd.rXqKEsd3pRSOoIyFAFIhbqf71BufwDfS3Guv21SlEx2"
>>> $user->save();
=> true
>>> App\User::first()->toArray();
=> [
       "id"         => "1",
       "name"       => "zhang jinglin",
       "email"      => "zjl@example.com",
       "created_at" => "2015-03-31 03:24:55",
       "updated_at" => "2015-03-31 03:24:55"
   ]
>>>
```



现在我们需要新发布的文章和用户关联，我们暂时先修改 `form_partial.blade.php` 来隐藏一个用户id，只是暂时：

```html
{{--临时处理--}}
{!! Form::hidden('user_id', 1) !!}
```



同时要修改模型的 `$fillabel` 属性，以便我们的 Mass Assisment。

```php
protected $fillable = [
       'title',
       'body',
       'published_at',
       'user_id' //临时设置
   ];
```



OK，添加一个文章。我们使用 `tinker` 来查看一下。

```bash
php artisan tinker
Psy Shell v0.4.1 (PHP 5.4.16 — cli) by Justin Hileman
>>> App\Article::first()->toArray();
=> [
       "id"           => "1",
       "user_id"      => "1",
       "title"        => "User 1 Article",
       "body"         => "User 1 Body",
       "published_at" => "2015-03-31 08:00:00",
       "created_at"   => "2015-03-31 04:17:58",
       "updated_at"   => "2015-03-31 04:17:58",
       "excerpt"      => null
   ]

#获取用户
>>> $user = App\User::first();
=> <App\User #0000000051cbb9d70000000073e11a3e> {
       id: "1",
       name: "zhang jinglin",
       email: "zjl@example.com",
       created_at: "2015-03-31 03:24:55",
       updated_at: "2015-03-31 03:24:55"
   }

#获取用户文章
>>> $user->articles()->toArray();
BadMethodCallException with message 'Call to undefined method Illuminate\Database\Query\Builder::toArray()'
>>> $user->articles->toArray();
=> [
       [
           "id"           => "1",
           "user_id"      => "1",
           "title"        => "User 1 Article",
           "body"         => "User 1 Body",
           "published_at" => "2015-03-31 08:00:00",
           "created_at"   => "2015-03-31 04:17:58",
           "updated_at"   => "2015-03-31 04:17:58",
           "excerpt"      => null
       ]
   ]
   
#为什么使用 $user->articles 而不是 #user->articles()?
#事实上，$user->articles()返回的是关系，如果你想用 articles() 你需要这样用
>>> $user->articles()->get()->toArray();
=> [
       [
           "id"           => "1",
           "user_id"      => "1",
           "title"        => "User 1 Article",
           "body"         => "User 1 Body",
           "published_at" => "2015-03-31 08:00:00",
           "created_at"   => "2015-03-31 04:17:58",
           "updated_at"   => "2015-03-31 04:17:58",
           "excerpt"      => null
       ]
   ]
   
#你只能使用 articles() 来进行下一步的工作，比如下面的查询

$user->articles()->where('title', 'User 1 Article')->get();

#我们也可以通过 article 获取 user

>>> $article = App\Article::first();
=> <App\Article #0000000051cbb9d60000000073e11a3e> {
       id: "1",
       user_id: "1",
       title: "User 1 Article",
       body: "User 1 Body",
       published_at: "2015-03-31 08:00:00",
       created_at: "2015-03-31 04:17:58",
       updated_at: "2015-03-31 04:17:58",
       excerpt: null
   }
>>> $article->user;
=> <App\User #0000000051cbb92d0000000073e11a3e> {
       id: "1",
       name: "zhang jinglin",
       email: "zjl@example.com",
       created_at: "2015-03-31 03:24:55",
       updated_at: "2015-03-31 03:24:55"
   }
>>>
```





## 认证

Laravel 出厂已经带有了用户认证系统，我们来看一下 `routes.php`，如果删除了，添加上：

```plain
Route::controllers([
    'auth' => 'Auth\AuthController',
    'password' => 'Auth\PasswordController'
]);
```



可以使用 `php artisan route:list` 查看一下。浏览器中访问 `/auth/login`，可以看到登陆界面，最好把系统默认的 `app.blade.php` 中关于 google 的东西注释起来，要不然你会疯掉的。

你可以使用 register、login甚至 forget password。

实际注册一个用户，提交后失败了，实际上没有失败，只是larave自动跳转到了 `/home`，我们已经删除了这个控制器。你可以使用 `tinker` 看一下，用户已经建立了。

在 `Auth\AuthController` 中实际上使用了 `trait`，什么是 triat？well，php只支持单继承，在php5.4中添加了trait，一个trait实际上是一组方法的封装，你可以把它包含在另一个类中。像是抽象类，你不能直接实例化他。

在 `Auth\AuthController` 中有对 trait 的引用：

```php
use AuthenticatesAndRegistersUsers;
```



让我们找到他，看一下注册后是怎么跳转的。他隐藏的挺深的，在 `vendor/laravel/framework/src/Illuminate/Foundation/Auth/AuthenticatesAndregistersUsers.php`，wow。

```php
public function redirectPath()
{
	if (property_exists($this, 'redirectPath'))
	{
		return $this->redirectPath;
	}
       
       //如果用户设置了 redirectTo 属性，则跳转到用户设置的属性，否则到home
	return property_exists($this, 'redirectTo') ? $this->redirectTo : '/home';
}
```



OK，我们知道了，只要设定 `redirectTo` 这个属性就可以自定义注册后的跳转了。我们在 `Auth\AuthContotroller` 中修改：

```php
protected $redirectTo = 'articles';
```



我们先使用 `/auth/logout` 确保我们退出，如果出错了不要害怕，我们没有默认的主页，重新访问：`auth/register` 新建一个用户，这次应该ok了。

再次logout，然后使用 login 登陆一下。

现在我们可以删除 `form_partial` 中临时设置的隐藏字段了，然后修改控制器：

```php
public function store(Requests\ArticleRequest $request) {
    //你可以这样
    //$request = $request->all();
    //$request['user_id'] = Auth::id();

    //更简单的方法
    $article = Article::create($request->all());
    //laravel 自动完成外键关联
    Auth::user()->articles()->save($article);

    return redirect('articles');
}
```



添加一个文章，然后使用 `tinker` 查看一下。

## 中间件

我们当然不希望任何人都能发布文章，至少是登陆用才可以。我们在控制器中添加保护：

```php
public function create() {
    if (Auth::guest()) {
        return redirect('articles');
    }
    return view('articles.create');
}
```



上面的代码可以工作，有一个问题，我们需要在每一个需要保护的方法中都进行上面的处理，这样做太傻了，幸好我们有中间件。

中间件可以理解为一个处理管道，中间件在管道中的某一时刻进行处理，这个时刻可以是请求也可以是响应。依据中间件的处理规则，可能将请求重定向，也可能通过请求。

在 `app/http/middleware` 中包含了三个中间件，名字就可以看出是干什么，好好查看一下，注意，`Closure $next` 代表了下一个中间件。

在 `app/http/kernel.php` 中对中间件进行登记。`$middleware` 段声明了对所有http都进行处理的中间件，`$routeMiddleware` 仅仅对路由进行处理，而且你必须显示的声明要使用这其中的某一个或几个中间件。

假设我们想对整个的 `ArticlesController` 进行保护，我们直接在构造函数中添加中间件：

```php
public function __construct() {
    $this->middleware('auth');
}
```



现在，任何方法都收到了保护。

但我们可能不想整个控制器都受到保护，如果只是其中的一两个方法呢？我们可以这样处理：

```php
public function __construct() {
    $this->middleware('auth', ['only' => 'create']);
    //当然可以反过来
    //$this->middleware('auth', ['except' => 'index']);
}
```



我们不一定在控制器的构造函数中引入中间件，我们可以直接在路由中声明：

```php
Route::get('about', ['middleware' => 'auth', 'uses' => 'PagesController@about']);
```



在 `kernel.php` 中提供的系统中间件，比如 `'Illuminate\Foundation\Http\Middleware\CheckForMaintenanceMode'` 是可以让我们进入到维护模式，比如系统上线了，但现在需要临时关闭一段时间进行处理，我们可以在命令行进行处理，看一下这个中间件的工作：

```bash
php artisan down
```



访问一下网站，可以看到任何 url 的请求都是马上回来。网站上线：

```bash
php artisan up
```



我们来做一个自己的中间件：

```bash
php artisan make:middleware Demo
```



然后添加代码：

```php
public function handle($request, Closure $next)
{
       //如果请求中含有 foo，我们就回到控制器首页
       if ($request->has('foo')) {
           return redirect('articles');
       }

	return $next($request);
}
```



如果希望在全部的请求使用中间件，需要在 `kernel.php` 中的 `$middleware` 中登记：

```php
protected $middleware = [
	...
	'App\Http\Middleware\Demo',

];
```



现在我们可以测试一下，假设我们访问 `/articles/create?foo=bar` ，我们被重定向到了首页。

让我们去除这个显示中间件，我们来创建一个真正有用的中间件。假设我们想保护某个页面，这个页面必须是管理者才能访问的。

```bash
php artisan make:middleware RedirectIfNotAManager
```



我们来添加处理代码：

```php
public function handle($request, Closure $next)
{
       if (!$request->user() || !$request->user()->isATeamManager()) {
           return redirect('articles');
       }

	return $next($request);
}
```



下面修改我们的模型：

```php
public function isATeamManager() {
    return false;
}
```



简单起见，我们直接返回false。这次我们把中间件放置在 `kernel.php` 中的`$routeMiddleware` 中。

```php
protected $routeMiddleware = [
	...
	'manager' => 'App\Http\Middleware\RedirectIfNotAManager',
];
```



我们做一个测试路由测试一下：

```php
Route::get('foo', ['middleware' => 'manager', function() {
    return 'This page may only be viewed by manager';
}]);
```



guest身份访问或者登录身份访问都会返回主页，但是如果修改 `isATeamManager()` 返回 `true`，登录身份访问可以看到返回的信息。

## 路由模型绑定

什么事路由模型绑定？让我们看一下下面的代码：

```php
public function show($id) {
    //我们需要自己查询依据 id 获取的模型
    $article = Article::findOrFail($id);

    return view('articles.show', compact('article'));
}
```



可以肯定的是，我们需要在很多的地方获取Id，然后找到模型，这个事情可以交给 Laravel 来做，这就是路由模型绑定。

我们需要先查看一下我们的路由：

```bash
php artisan route:list
```



我们可以看到生成的匹配符 `articles/{articles}`，修改 `app/provider/RouteServiceProvider.php`

```php
public function boot(Router $router)
{
	parent::boot($router);

	//绑定路由模型 匹配符是 {articles}
       $router->model('articles', 'App\Article');

}
```



有了上面的处理，我们在路由中获取到的就不在是id了，而是 Article 对象。修改控制器：

```php
public function show(Article $article) {
    //有了路由模型绑定，我们不需要自己查找数据了
    return view('articles.show', compact('article'));
}
```



So cool! 修改其他方法：

```php
public function edit(Article $article) {
    return view('articles.edit', compact('article'));
}

public function update(Article $article, Requests\ArticleRequest $request) {
    $article->update($request->all());

    return redirect('articles');
}
```



当然，我们有完全的控制路由模型如何绑定：

```php
public function boot(Router $router)
{
	parent::boot($router);

	//如果你需要控制模型的生成，可以这样
       $router->bind('articles', function($id) {
           return \App\Article::published()->findOrFail($id);
       });
}
```


---

> 作者: [张景林](https://zhangjinglin.github.io/about/)  
> URL: https://zhangjinglin.github.io/2016/02/laravel5/  

