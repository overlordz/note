### 安装

> composer create-project --prefer-dist laravel/lumen project_name





#### 功能缺失完善

lumen没有php artisan make:controller等命令的解决方法

安装 lumen 后，使用 php artisan make:controller XXX 时出错。于是，查看了下相关的命令（php artisan list），发现对比 laravel 缺失了很多命令。

遇到这个问题的，都是使用过 lumen的。遇到这个问题的，也有人解决这个问题。请看：https://github.com/webNeat/lumen-generators

github 上已经介绍了怎么安装使用。这里再啰嗦一下。

安装
```
composer require wn/lumen-generators
```
注册服务。
在 app/Providers/AppServiceProvider.php 中添加：
```
public function register()
{
    if ($this->app->environment() == 'local') {
        $this->app->register('Wn\Generators\CommandsServiceProvider');
    }
}
```
在 bootstrap/app.php 中注册。添加：
```
$app->register(Wn\Generators\CommandsServiceProvider::class);
```
再次查看和使用。

> php artisan list 	//已经追加了一些新命令

> php artisan wn:controller Lover 	//创建控制器，连带的路由也创建了
