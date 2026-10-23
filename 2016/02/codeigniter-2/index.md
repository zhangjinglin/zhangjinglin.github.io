# CodeIgniter教程 - CRUD


CRUD - Create，Read，Update，Delete 在这篇教程中，我们学习CI对数据库的操作。

## 查询数据库

在前面的教程中，我们使用下面的语句来获取`users`表中的数据

```php
$query = $this->db->get('users');
```



我们来换一种方式来查询数据库，

```php
$query = $this->db->query('select * from users');
return $query->result();
```



结果是相同的，这样我们就可以利用我们的SQL知识来查询我们的数据。你可以使用CI的用户手册来查看数据库的功能，比如我们可以使用`$query->num_rows()`来返回获取的记录数，`$query->num_fields()`返回字段数等。

## 带有参数的查询

```php
//获取姓名为 'Zhang Jinglin' 的用户
 $query = $this->db->where('name', 'Zhang Jinglin')->get('users');
```



 然我们通过URL来获取参数。首先修改我们的模型，`User_model.php`

```php
 <?php
/**
 * Created by PhpStorm.
 * User: jingl
 * Date: 2016/2/11
 * Time: 13:46
 */

class User_model extends CI_Model
{
    public function get_users($id)
    {
        $query = $this->db->where('id', $id)->get('users');
        return $query->result();
    }
}
```



在模型的方法中，我们需要获取用户的ID，依据ID来读取用户数据。修改控制器，`User.php`

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
    public function index($id = 0)
    {
        $this->load->model('user_model');
        $data['results'] = $this->user_model->get_users($id);
        $this->load->view('user_index', $data);
    }
}
```



方法中的参数是赋值为默认值，如果没提供参数，默认值为0。现在我们访问`http://localhost/ci/index.php/user/index/1`就能获取id为1的用户，你可以修改不同的id查看一下。

根据上面的结果，实际上CI的URL模式是`index.php/{控制器}/{方法}/{id}`。

实际上，我们可以在`where`方法中传入一个数组，作为参数

```php
$this->db->where([
    'id' => $id,
    'name' => $name
])
```



上面的参数相当于`where id = $id and name = $name`。  当然你可以查看用户手册来学习更多的知识。

如果你更愿意使用原始的SQL语句来进行查询，（事实上我是非常喜欢用SQL语句来查询的），这里面我们来看一如何使用查询绑定来简化我们的查询工作。

比如下面的例子：

```php
$sql = "SELECT * FROM some_table WHERE id IN ? AND status = ? AND author = ?";
$this->db->query($sql, array(array(3, 6), 'live', 'Rick'));
```





## 插入数据

插入数据也是非常简单的，我们在`User_model.php`中增加方法

```php
public function create_user($data)
{
    $this->db->insert('users', $data);
}
```



模型方法中的参数接受一个数组，这个数组包含对应的字段的值。在`User.php`控制器中条件方法

```php
public function create()
 {
     $this->load->model('user_model');
     
     $name = 'Zhang San';
     $password = '123';
     $this->user_model->create_user([
         'name' => $name,
         'password' => $password
     ]);
     echo 'Insert OK';
 }
```



这个方法将需要构建的记录的字段复制到数组中，交给模型方法执行即可。在浏览器中访问`http://localhost/ci/index.php/user/create`，如果看见了正常的输出，就可以查看数据库了。

上面的控制器方法中有一个问题，就是在不同的方法中我们都需要装载模型

```php
$this->load->model('user_model');
```



当然可以利用自动装载来完成（在上一篇教程中），但是我是不大喜欢在自动装载中装入模型类的，因为这样就对系统的全部类可见了，那么怎么解决呢？解决的方法是利用构造函数即可。在控制器`User.php`中添加构造方法。

```php
public function __construct()
{
    parent::__construct();
    $this->load->model('user_model');
}
```




> 上面的构造方法中，千万不要忘记了调用父类的构造方法。

通过在构造方法中装载模型类，我们就可以在`index create`方法中取消装载模型的语句了。

## 修改数据

一般修改记录会修改某一条记录，这可以用`where`方法来进行限定。修改我们的模型类，添加方法

```php
public function update_user($id, $data)
{
    $this->db->where('id', $id)->update('users', $data);
}
```



我们的模型方法接受两个从参数，一个是要修改的记录ID，一个是要修改的数据。在控制器中增加修改方法：

```php
public function update()
{
    $id = 3;
    $name = 'Modify Name';
    $this->user_model->update_user($id, [ 'name' => $name ]);
}
```



注意到了么，因为我们在构造函数中装载了模型类，所以在控制其方法中就不要显示的装载模型了。

## 删除数据

一般删除记录会删除某一条记录，这可以用`where`方法来进行限定。修改我们的模型类，添加方法

```php
public function delete_user($id)
{
    $this->db->where('id', $id)->delete('users');
}
```



我们的模型方法接受要删除的记录ID。在控制器中增加修改方法：

```php
public function delete()
{
    $id = 3;
    $this->user_model->delete_user($id);
}
```



这就是CI的CRUD，当然CRUD也有其他的方式，我们后面会学习。


---

> 作者: [张景林](https://zhangjinglin.github.io/about/)  
> URL: https://zhangjinglin.github.io/2016/02/codeigniter-2/  

