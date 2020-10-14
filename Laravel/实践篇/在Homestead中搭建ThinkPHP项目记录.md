### 添加 serve 配置

在 `Homestead` 的 `script` 文件夹中添加一个 `serve-tp.sh` 文件。内容如下：

```
 #!/usr/bin/env bash
    declare -A params=$6     # Create an associative array
    paramsTXT=""
    if [ -n "$6" ]; then
       for element in "${!params[@]}"
       do
          paramsTXT="${paramsTXT}
          fastcgi_param ${element} ${params[$element]};"
       done
    fi

    block="server {
        listen ${3:-80};
        listen ${4:-443} ssl http2;
        server_name .$1;
        root \"$2\";

        index index.html index.htm index.php;

        charset utf-8;

        location / {
            #try_files \$uri \$uri/ /index.php?\$query_string;
            if (!-e \$request_filename) {
               rewrite  ^(.*)$  /index.php?s=/\$1  last;
               #break;
            }
        }

        location = /favicon.ico { access_log off; log_not_found off; }
        location = /robots.txt  { access_log off; log_not_found off; }

        access_log off;
        error_log  /var/log/nginx/$1-error.log error;

        sendfile off;

        client_max_body_size 100m;


        location ~* ^(/images|/Static).+.(jpg|jpeg|css|gif|png|ico) {
            access_log              off;
        }

        location ~ \.php$ {
            fastcgi_split_path_info ^(.+\.php)(/.+)$;
            fastcgi_pass unix:/var/run/php/php7.1-fpm.sock;
            fastcgi_index index.php;
            include fastcgi_params;
            fastcgi_param  SCRIPT_FILENAME  \$document_root\$fastcgi_script_name;
            fastcgi_intercept_errors off;
            fastcgi_buffer_size 16k;
            fastcgi_buffers 4 16k;
            fastcgi_connect_timeout 300;
            fastcgi_send_timeout 300;
            fastcgi_read_timeout 300;
            include fastcgi_params;
        }

        location ~ /\.ht {
            deny all;
        }

        ssl_certificate     /etc/nginx/ssl/$1.crt;
        ssl_certificate_key /etc/nginx/ssl/$1.key;
    }
    "

    echo "$block" > "/etc/nginx/sites-available/$1"
    ln -fs "/etc/nginx/sites-available/$1" "/etc/nginx/sites-enabled/$1"
```

在 `Homestead.yaml` 中，

```
 - map: 你的域名
      to: /home/vagrant/code/你的项目/public
      type: tp
```

修改之后需要重启 vagrant 才能生效