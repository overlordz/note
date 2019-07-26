
## 多语言

#### 语言包

默认表单提示是英文的，我们可以安装中文语言包进行汉化。

```
    composer require composer require caouecs/laravel-lang:~4.0
```

包含大多数语言，语言包位于`vendor/caouecs/larvel-lang/src` 目录中。

**使用**

1. 根据需要复制语言包到 `resources/lang` 目录中。

2. 修改 `config/app.php` 配置文件

   ```
   'locale' => 'zh-CN',
   ```

#### JSON语言包

在 `resources/lang` 目录下定义 `zh-CN.json` 文件

```
{
  "Login": "登录",
  "Logout":"退出",
  "E-Mail Address": "邮箱",
  "Register":"注册",
  "Password":"密码",
  "Confirm Password":"确认密码",
  "Name":"帐号",
  "Remember Me":"记住我",
  "Forgot Your Password?":"找回密码",
  "Reset Password":"重置密码",
  "Send Password Reset Link":"发送重置邮件",
  "Reset Password Notification":"重置密码通知",
  "You are receiving this email because we received a password reset request for your account.":"您收到这封邮件是因为我们收到您的帐户密码重置请求。",
  "If you did not request a password reset, no further action is required.":"如果没有要求重新设置密码，则不需要进一步的操作。"
}
```

在模板中就可以使用 `{{__('Login')}}`  调用了，Laravel 默认的登录模板大量使用了 JSON 语言包