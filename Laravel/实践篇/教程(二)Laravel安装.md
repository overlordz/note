### 安装命令
```
如果没有安装composer，则安装
composer install

转为国内中文地址保存
composer config -g repo.packagist composer https://packagist.phpcomposer.com

下载指定laravel版本
composer create-project --prefer-dist laravel/laravel blog "5.8.*"

#Tips：安装完 Laravel 后，你可能需要给这两个文件配置读写权限：
storage 目录和 bootstrap/cache 目录应该允许 Web 服务器写入，否则 Laravel 将无法运行
```



### Nginx 的配置

> 把public文件下的index.php为根目录



### 如果访问报错，应该更新一下应用秘钥

> php artisan key:generate



### 基本配置修改

```
修改根目录中：' .env '和 ' config/database.php ' ;

.evn 修改内容：
原：	  
    DB_HOST=127.0.0.1
    DB_PORT=3306
    DB_DATABASE=homestead
    DB_USERNAME=homestead
    DB_PASSWORD=secret

修改后：  
    DB_HOST=127.0.0.1
    DB_PORT=3306
    DB_DATABASE=laravel
    DB_PREFIX=blog_
    DB_USERNAME=root
    DB_PASSWORD=123456

.database.php修改内容：
    原： 'prefix' => '',
    修改后：  'prefix' => env('DB_PREFIX', ''),

```



### 模版引用

```
1、把前端模版放置在resources/views/新建文件名/样式文件
2、控制文件方法调用 return view('admin.login');
3、样式路径的更改（{{asset}}）：
    {{asset('resources/views/admin/style/css/ch-ui.admin.css')}}

4、html 中img图片地址引用（{{url}}）：{{url('admin/code')}}
例如图片验证码：
<img src="{{url('admin/code')}}" alt="" onclick="this.src='{{url('admin/code')}}?'+Math.random()">

```



### 第三方类的引用

```
验证类
1、放到类库目录，如果没有就新建（'resources/新建名称（org）/code'）;
2、引用地址：require_once 'resources/org/code/Code.class.php';
3、类的调用：
$code=new \Code;
$code->make();
注意：如果直接new  类class  会直接当前目录去找（报错），而是应该这样写：new \类class从底层上去找，这样才不会出错；
```