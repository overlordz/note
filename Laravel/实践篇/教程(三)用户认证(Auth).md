> Laravel 版本号：5.8



## 用户认证

#### Laravel提供快速搭建认证所需的所有路由和视图

	php artisan make:auth
	
	执行数据创建：
	
	php artisan migrate



#### 自定义路径

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



#### 自定义登录用户名

Laravel 默认使用 email 字段来认证。如果你想使用其他的字段，可以在 LoginController 控制器里面定义一个 username 方法：


```php
public function username()
{
	// 提交名要对应
    return 'name'; 
}
```



#### 自定义看守器

你还可以自定义用户认证和注册的 「看守器」，**用于前后台登录使用不同的用户组**。要实现这一功能，需要在 `LoginController`,`RegisterController` 和 `ResetPasswordController` 中定义 `guard` 方法。该方法会返回一个看守器实例：

```php
use Illuminate\Support\Facades\Auth;

protected function guard()
{
    return Auth::guard('guard-name');
}
```



#### 注意事项：

```
1、laravel中不使用 remember_token时退出报错，如何解决？
    解决办法：
    1、在相关模型添加：protected $rememberTokenName = '';
    2、protected $hidden = []; 删掉remember_token
```