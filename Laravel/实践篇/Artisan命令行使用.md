> 参考文章链接：https://learnku.com/docs/laravel/6.x/artisan/5158#defining-input-expectations



#### 生成命令

该命令会在 `app/Console/Commands` 目录中创建一个新的命令类

```shell
php artisan make:command SendEmails
```



#### 命令结构

```php
// 命令行的名称及签名
protected $signature = 'email:send {paramsName} {paramsNameTwo}';
// 可选参数...
email:send {user?}
// 带有默认值的可选参数...
email:send {user=foo}

// 命令行的描述
protected $description = 'Send drip e-mails to a user';

// 执行命令行
public function handle()
{
    /**** 逻辑代码处理部分 ****/
    // 接收所有参数并返回数组格式
    $allArgument = $this->argument();
	// 获取某个参数值
    $oneArgument = $this->argument('paramsName');
}
```

执行命令如下

> php artisan email:send 8888@qq.com 纯文本邮件测试



#### artisan命令查看

> php artisan list