

> Laravel 查询数据库获取结果实现判断是否为空

```php
$users = DB::table('users')->where('id',$id)->get();
// 发现直接使用 is_null 或 empty 是无法判段它结果集是否为空的，因为 Laravel 即使取到的空结果集，Eloquent 仍然会返回object(Illuminate\Support\Collection)对象实例。

if(is_null($users)){}
或
if(empty($users)){}
```

根据文档可以了解，Eloquent 已经给我们封装几个判断方法如下：

```php
$users = DB::table('users')->where('id',$id)->get();
if ($users->first()) {} 
或
if (!$users->isEmpty()) {} 
或
if ($users->count()) {}
```



> Laravel 解决 groupBy 时出现的错误 isn't in Group By问题

```
原因：
1、mysql从5.7以后，默认开启group by的严格模式
2、Laravel中 database.php 配置中的 strict 配置问题， 因为在配置中配置为true的时候，laravel的groupBy会为所有的例分组， 设置为false之后，他只会为你指定的列分组，就可以解决这个问题。

报错内容：
Illuminate\Database\QueryException  : SQLSTATE[42000]: Syntax error or access violation: 1055 'shopSys.helper_oil.id' isn't in GROUP BY (SQL: select `id`, `car_brand`, `car_line`, `car_produceYear`, `car_cc`, `factory`, `engine_model`, `engine_cc`, `oil_init`, `oil_top` from `helper_oil` where `id` > 0 group by `car_brand`, `car_line`, `car_produceYear`, `car_cc`, `factory`, `engine_model`, `engine_cc`, `oil_init`, `oil_top` limit 10)
```

处理方式：

```
更改 database.php 配置 mysql
'mysql' =>[
	'strict' => false,
]
```





#### 插入数据

> Model::create()

成功返回值是对象



> model::insert()

成功返回值是bool1



#### Laravel 定时任务 crontab 一直不执行

> crontab环境变量和系统变量不一致问题，建议使用完整路径

参考文章路径：https://learnku.com/laravel/t/45687

```
有问题：
* * * * * cd /www/item-dir && php artisan schedule:run >> /dev/null 2>&1
执行正常的：
* * * * * cd /www/item-dir && /usr/local/bin/php artisan schedule:run >> /dev/null 2>&1
```



