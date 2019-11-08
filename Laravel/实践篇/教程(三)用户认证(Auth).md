> Laravel 版本号：5.8

## 用户认证

##### Laravel提供快速搭建认证所需的所有路由和视图

	php artisan make:auth
	
	执行数据创建：
	
	php artisan migrate



##### 自定义路径

当用户认证成功，他们会被重定向到 `/home` 这个 URI 下。你可以在 `LoginController`，`RegisterController`， `ResetPasswordController`，还有 `VerificationController` 控制器中定义 `redirectTo` 属性来自定义验证后的重定向位置：

```php
// 方式一：
protected $redirectTo = '/';
```

接下，你应该修改 `RedirectIfAuthenticated` 中间件中的 `handle` 方法，以便在重定向用户时重定向到新的 URI。

如果重定向路径需要自定义生成逻辑，你可以定义 `redirectTo` 方法替代 `redirectTo` 属性：

```php
// 方式二：
protected function redirectTo()
{
    return '/path';
}
```

> {提示} redirectTo 方法优先于 redirectTo 属性。



##### 自定义登录用户名

Laravel 默认使用 email 字段来认证。如果你想使用其他的字段，可以在 LoginController 控制器里面定义一个 username 方法：


    public function username()
    {
    	// 提交名要对应
        return 'name'; 
    }

