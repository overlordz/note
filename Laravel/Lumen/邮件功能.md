#### 安装

> composer require "illuminate/mail"

该在 `bootstrap/app.php` 文件中注册 `Illuminate\Mail\MailServiceProvider`：

```
$app->register(Illuminate\Mail\MailServiceProvider::class);
```



接下来，通过将 mail.php 配置文件从 完整的 Laravel 框架 复制到项目根目录中的 config 目录来配置邮件选项。当然，您可以根据需要自由调整配置选项。如果 Lumen 应用程序不包含 config 目录，则应创建它。

创建配置文件后，应在 bootstrap/app.php 文件中注册 mailer 及其别名：

```
$app->configure('mail');
$app->alias('mail.manager', Illuminate\Mail\MailManager::class);
$app->alias('mail.manager', Illuminate\Contracts\Mail\Factory::class);
$app->alias('mailer', Illuminate\Mail\Mailer::class);
$app->alias('mailer', Illuminate\Contracts\Mail\Mailer::class);
$app->alias('mailer', Illuminate\Contracts\Mail\MailQueue::class);
```



Lumen 应用程序的 `.env` 件还应提供以下配置选项：

```
MAIL_DRIVER=smtp
MAIL_HOST=smtp.exmail.qq.com
MAIL_PORT=465
MAIL_USERNAME=companyEmail@qq.com
MAIL_PASSWORD=EmailPassword
MAIL_ENCRYPTION=ssl
MAIL_FROM_ADDRESS=sendEmail@qq.com
MAIL_FROM_NAME="emailName"
```



### 使用

```
        Mail::raw('test mail', function ($m) {
            $m->to('xxxxxx@qq.com')->subject('test email');
        });
```





## 问题解决



#### lumen框架 A facade root has not been set 报错的解决办法

找到`\bootstrap\app.php` 文件的26行和28行,去掉前边的注释就ok

```
$app->withFacades();

$app->withEloquent();
```