# ThinkPHP的使用

## 打开debug模式

找到config目录下的app.php文件 设置如下

```sh
 'show_error_msg'         => true,
```

没正式上线之前打开debug模式,方便调试,上线后再关闭

## 新建 Controller 命令

### 普通控制器

```sh
# 注意 Controller 的名字(在这里是Article)要大写
D:\xampp\htdocs\thinkphp5> php think make:controller index/Article --plain
```

### 资源控制器

```sh
# 相对于普通控制器 没有 --plain
D:\xampp\htdocs\thinkphp5> php think make:controller index/Article
```

## 路由

### 路由到自定义方法

```php
// 路由到自定义的方法
// 第一个参数是定义的路由规则
// 第二个参数是调用的方法 或 控制器中的方法
Route::get("hello",function ()
{
    return "Hello World!";
});
```

### 路由到控制器的方法

```php
// 路由到控制器的方法
// 如果设置了路由器访问指定方法的时候,就只能通过路由形式访问,不能再使用 模块/控制器/方法名 的方式来访问
Route::get("article",'index/article/index');
```

### 资源路由

```php
// 资源路由
// 第一个参数是定义路由规则
// 第二个参数是该规则去找的控制器名称
// 资源路由会自动根据资源路由的定义方式,生成对应的每个方法的请求方式及路由地址
Route::resource('student','index/Student');
```

### 有参数的路由

```php
// 如果在定义路由的时候,需要传递一些参数,可以使用 : 来定义参数 该参数会自动的被传递给当前路由所调用的方法,作为方法的参数自动被接收
Route::get('hello/:name', 'index/hello');
```

## View模板

### 建立模板

- 在当前模块目录下新建view文件夹
- 在view文件夹下新建对应控制器名字的文件夹 名字全部小写
- 在控制器文件夹下新建对应控制器方法名字的html文件即为模板文件

### 使用模板

控制器代码如下

```php
class Index
{
    public function index()
    {
        return view();
    }

    public function hello($name = 'ThinkPHP5')
    {
        return 'hello,' . $name;
    }
}
```

### 安装数据迁移插件

更新国内源

```sh
composer config -g repo.packagist composer https://packagist.phpcomposer.com
```

安装

```sh
composer require topthink/think-migration
```

如果报错

```sh
composer config -g -- disable-tls true
```
