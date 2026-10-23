# CodeIgniter教程 - 登录项目


我们来完成一个完整的登录项目。

## 准备工作

我们不会从头创建我们的样式，我们使用流行的CSS框架Bootstrap来进行构建。下载bootstrap，然后在项目中新建一个目录，目录的名字叫做`public`可能是一个好的选择。在`public`下面，新建文件夹`css fonts js`，参考下面的图片，别忘了必备的`jQuery`。

![](/2016/02/codeigniter-3/01.png)

## 创建模板

我们不想每个页面都从头构建，最佳的方式是创建模板。创建`views\layouts`目录。

在模板文件中如何引入css和js文件呢？路径是什么？我们可以使用CI提供的`base_url()`函数来获取项目的URL，当然这个值你也可以在`config.php`中进行修改。引入css类似这样的语句：

```php
<link rel="stylesheet" href="<?=base_url()?>public/css/bootstrap.min.css">
```




> base_url()函数返回的结果带有`/`路径，所以在`public`之前不要在添加`/`，以免画蛇添足。

这个函数不是马上就能使用的，它是CI提供的URL辅助功能，需要在`autoload.php`中开启这个支持。

```php
$autoload['helper'] = array('url');
```



在下面创建布局文件，我们起名叫做`main.php`。

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Login Project</title>
    <link rel="stylesheet" href="<?=base_url()?>public/css/bootstrap.min.css">
    <script src="<?=base_url()?>public/js/jquery-2.2.0.min.js"></script>
    <script src="<?=base_url()?>public/js/bootstrap.min.js"></script>
</head>
<body>
<div class="container">
    <div class="col-xs-3">
        this is the side bar.
    </div>
    <div class="col-xs-9">
        <?php $this->load->view($content_view); ?>
    </div>
</div>
</body>
</html>
```



上面的代码一目了然，最关键的就是引入内容视图的语句，那么如何给模板传送数据呢？我们修改我们的`Home.php`

```php
<?php
/**
 * Created by PhpStorm.
 * User: jingl
 * Date: 2016/2/11
 * Time: 13:27
 */

class Home extends CI_Controller
{
    public function index()
    {
        $data['content_view'] = 'home_index';
        $this->load->view('layouts/main', $data);
    }
}
```



在控制器的index方法中，我们返回的是模板文件，但是我们传入了要现实的内容视图的变量，这样就完成了模板和内容的结合。

`home_index.php`的内容如下：

```html
<h1>Hello, CodeIgniter!</h1>
```



是的，就这么简单，其他的已经通过模板来完成了。访问`http://localhost/ci/index.php/home`应该可以看到结果，打开浏览器控制台，确保没有错误，所有的css和js正确加载了。

## 侧边栏

在侧边栏我们想显示登录窗口，这就涉及到了CI的表单控制功能，所以我们要在`autoload.php`中开启我们的表单辅助功能。

```php
$autoload['helper'] = array('url', 'form');
```



我们在`views`目录下创建子目录`user`，然后创建文件`login_view.php`。

```html
<h2 class="text-center">登录</h2>
<?=form_open('user/login', ['id' => 'login_form', 'class' => ''])?>

<div class="form-group">
    <input type="text" name="name" class="form-control" placeholder="用户名"><br>
    <input type="password" name="password" class="form-control" placeholder="密码"><br>
    <input type="submit" value="Login" class="btn btn-block btn-primary">
</div>

<?=form_close()?>
```



我们使用了`form_open`和`form_close()`辅助函数，当然可以直接使用普通的HTML表单方式，但是推荐用这种方式以避免书写完整的URL提交地址。在CI的表单辅助函数中，也包含了一些字段的辅助函数，上面的HTML字段也可以替换成下面的辅助函数，使用哪个，取决于你自己。

```html
<?=form_input(['id' => 'name', 'name' => 'name', 'class' => 'form-control', 'placeholder' => '用户名'])?>
<?=form_password(['id' => 'password', 'name' => 'password', 'class' => 'form-control', 'placeholder' => '密码'])?>
<?=form_submit(['name' => 'submit', 'value' => '登录', 'class' => 'btn btn-primary btn-block'])?>
```



在我们的表单中，我们声明了表单的提交地址是`user/login`，就是User控制器的login方法。在`User.php`控制器中添加`login`方法。

```php
<?php
/**
 * Created by PhpStorm.
 * User: jingl
 * Date: 2016/2/11
 * Time: 13:50
 */

class User extends CI_Controller
{
    public function __construct()
    {
        parent::__construct();
        $this->load->model('user_model');
    }

    public function login()
    {
        echo $_POST['name'];
        echo '<br>';
        echo $_POST['password'];
    }
}
```



目前我们的登录方法仅仅是简单的将输入数据显示出来。测试一下吧。login方法可以使用CI的功能来进行一致化，修改代码：

```php
public function login()
{
    echo $this->input->post('name');
    echo '<br>';
    echo $this->input->post('password');
}
```





## 表单数据验证

CI提供了表单验证的功能，帮助我们来进行数据的验证。首先，要在`autoload.php`中装载表单验证

```php
$autoload['libraries'] = array('database', 'form_validation');
```



下面我们就可以修改`User.php`中的`login`方法，让它支持验证了。

```php
public function login()
{
    $this->form_validation->set_rules('name', '用户名', 'trim|required|min_length[3]');
    $this->form_validation->set_rules('password', '密码', 'trim|required|min_length[3]');

    if ($this->form_validation->run() == FALSE) {
        print_r(validation_errors());
    }
}
```



上面的rules是CI提供的验证规则，trim是去掉输入的空格，required是必须提供输入，min_length是最小的输入长度，实际上CI提供的rules非常多，在CI的类库参考-表单验证类中可以找到全部的规则。你可以尝试着什么也不输入直接提交，或者输入一个自负的情况下提交。

上面的处理有一个问题，输出的错误信息是英文，我们不能指望我们的访问者读懂英语，毕竟我们说中文。那要怎么处理呢？这就涉及到了CI应用的语言问题。解决这个有几种方案。

第一，在`application\config\config.php`中，我们可以把`$config['language'] = 'english';`修改为`chinese`表示我们要说中文。如果现在你在重新提交表单，你会发现CI出现了错误，错误信息是`找不到语言文件 language/chinese/form_validation_lang.php`。这个文件我们怎么创建呢？我们复制`system/language/english/form_validation-lang.php`到目录`application/language/chinese/`下，什么，没有这个目录？你自己创建一个就完了。我们修改一个提示坐下测试

```php
$lang['form_validation_required']		= '{field} 必须输入';
```



现在表单里面什么也不写，直接提交就看到中文的错误提示。

第二种方法，就是我们只需要几个中文的错误信息，没必要向上一种方法那么复杂，而且有些错误信息要单独的提示，怎么办？我们可以直接在可能输出错误信息的方法里面提供我们自己的错误说明。

```php
public function login()
{
    $this->form_validation->set_rules('name', '用户名', 'trim|required|min_length[3]', [
        'required' => '你不写用户名我知道你是谁',
        'min_length' => '没哟这么短的名字'
    ]);
    $this->form_validation->set_rules('password', '密码', 'trim|required|min_length[3]');

    if ($this->form_validation->run() == FALSE) {
        print_r(validation_errors());
    }
}
```



也就是我们直接在规则里面添加我们的错误信息即可。你愿意用哪种方法你自己随意吧。

获取到了错误信息，我们不能直接这么粗暴的显示出来，正常的情况是我们返回登录页面然后在上面显示错误信息。但这就涉及到了跳转，也就是从User控制器跳转到Home控制器，然后传送我们的错误信息，我们需要在`autoload.php`中开启session。

```php
$autoload['libraries'] = array('database', 'form_validation', 'session');
```



在CI中可以使用session中的flashdata作为一次性的输出信息，也就是输出一次，下次请求就没有了。

```php
public function login()
{
    $this->form_validation->set_rules('name', '用户名', 'trim|required|min_length[3]', [
        'required' => '你不写用户名我知道你是谁',
        'min_length' => '没哟这么短的名字'
    ]);
    $this->form_validation->set_rules('password', '密码', 'trim|required|min_length[3]');

    if ($this->form_validation->run() == FALSE) {
        $data['errors'] = validation_errors();
        $this->session->set_flashdata($data);
        redirect('home');
    }
}
```



上面的代码一目了然，没啥可说的。下面我们就要在我们的视图中添加对错误信息的处理了。

```html
<h2 class="text-center">登录</h2>

<?php if ($this->session->flashdata('errors')): ?>
    <?= $this->session->flashdata('errors') ?>
<?php endif ?>

<?=form_open('user/login', ['id' => 'login_form', 'class' => ''])?>
```



现在在进行登录测试，就可以看到错误信息了。

获取到正确的数据就可以进行正式的登录处理了，好了，这篇教程就到这里了，希望你喜欢。


---

> 作者: [张景林](https://zhangjinglin.github.io/about/)  
> URL: https://zhangjinglin.github.io/2016/02/codeigniter-3/  

