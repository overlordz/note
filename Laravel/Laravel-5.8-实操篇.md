## 用户认证

##### Laravel提供快速搭建认证所需的所有路由和视图

	php artisan make:auth



##### 自定义登录用户名

Laravel 默认使用 email 字段来认证。如果你想使用其他的字段，可以在 LoginController 控制器里面定义一个 username 方法：


    public function username()
    {
    	// 提交名要对应
        return 'name'; 
    }
