#### laravel报错 No query results for model [App\Post]. 的解决方法

这个通常由路由绑定出的问题，注意有绑定模型的路由，同路径的路由需要放在没绑定路由的后面

例如：/posts/create 和/posts/{post}的是同路径，/posts/{post}必须放在/posts/create 后面

```php
Route::get('/users/info','UserController@info')->name('users.info');
Route::get('/users','UserController@index')->name('users.index');
Route::get('/users/{user}','UserController@show')->name('users.show');
```



#### Laravel 访问报错 500，但是不显示错误信息

查看项目中是否存在 `.env` 文件，添加文件后执行：`php artisan key:generate`



#### 在laravel数据操作中having连缀分页函数paginate出现错误

> [参考链接](https://segmentfault.com/q/1010000007832996/a-1020000020188157)

```
执行Sql语句：
$users = User::where('uid',$userId)
        ->groupBy('unionid')
        ->orderBy('updated_at','desc')
        ->select('unionid',DB::raw('count(*) as total'))
        ->having('total','>','0')
        ->paginate(10);
```



曝出下面提示错误：

> Unknown column 'counts' in 'having clause' ......



解决方式：

1、抛弃 `paginate()`直接使用  `get()`

2、将 `having` 改为 `havingRaw`

```
$users = User::where('uid',$userId)
        ->groupBy('unionid')
        ->orderBy('updated_at','desc')
        ->select('unionid')
        ->havingRaw('count(*) > 0')
        ->paginate(10);
```

