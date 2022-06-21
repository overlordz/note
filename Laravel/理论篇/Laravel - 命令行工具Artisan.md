#### 查看所有可用的Artisan的命令（list）

```
    php artisan
    php artisan list
```

#### 查看命令的帮助信息（help）

```
    php artisan help migrate
```

#### Artisan 基本使用

```
创建控制器
    php artisan make:controller StudentController

创建模型
    php artisan make:model Student
    php artisan make:model Models/TodoList (具体目录)
    
创建中间件
    php artisan make:middleware Activity
    
实例：创建资源路由控制器和模型文件
    php artisan make:controller Admin/ArticleController --resource --model=Article
    
路由相关
    查看所有路由
    php artisan route:list
    缓存路由
    php artisan route:cache
    php artisan route:clear

清除缓存
    清除视图缓存
        php artisan view:clear
    清除运行缓存
        php artisan cache:clear
    清除配置缓存
        php artisan config:clear
    
migrate
    创建migration文件
    php artisan make:migration create_user_table
    执行migrate迁移【所有】
    php artisan migrate
    执行migrate迁移【指定文件】
    php artisan migrate --path=./database/migrations/2022_06_10_000032_create_grow_plants_table.php
    
    *注意：迁移执行顺序是依据migration文件的文件名时间戳前缀来依次执行的，所以如果某个*表存在外键依赖关系，要保证被依赖的表先被migrate。
    *注意：有些时候迁移可能会出现异常，比如说“Class not found”或者“No such file or directory”，可以尝试先执行composer dump-autoload命令后再进行迁移。
    
tinker
    tinker是一个交互工具，进入tinker模式就好比进入了一个php的shell，我们可以在这个“shell”上直接敲php代码并执行获得反馈。
    php artisan tinker
    
任务队列
    php artisan queu:listen 持续监听

    php artisan queue:work 执行头一个job
    
    *注意：php artisan queue:work –daemon 并不是后台运行=。=# 而是表示持续执行work，官方文档推荐用这个取代listen，
    因为daemon worker不会每次重启框架，所以速度快，但你要个更注意程序中的内存问题，不处理好容易导致worker占用越来越大越来越大的内存。。。
    另外因为不会重启框架，所以期间如果修改了代码，必须restart worker
    
vendor:publish
    执行第三方包的publish指令。publish指令的主要作用是拷贝第三方包的assets文件（可能是config文件、view文件等）到项目目录中。publish指令通常定义在xxxServiceProvider.php的boot()方法中。

    以zizaco/entrust包为例，在EntrustServiceProvider.php中定义的publish指令，其意义是拷贝entrust包内的config/config.php文件到项目配置目录（app/config/）下，并命名为entrust.php。	
    
    // 执行所有第三方包的publish指令
    php artisan vendor:publish
     
    // 执行某个ServiceProvider的publish指令
    php artisan vendor:publish --provider="Zizaco\Entrust\EntrustServiceProvider"
```