## Mailhog

Mailhog (`homestead` 已经内置）可以轻松的抓取到你发送的电子邮件并进行检查，而无需将邮件真正发送给收件人. 开始之前, 请更新你的 `.env` 文件并使用如下邮件设置:

```
MAIL_DRIVER=smtp
MAIL_HOST=localhost
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
```

**访问**

```
http://domain:8025
```

