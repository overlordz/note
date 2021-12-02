### Nginx 曝出 413 错误码

client_max_body_size 20M;

client_body_buffer_size 128k;

fastcgi_intercept_errors on;



### php.ini修改php上传文件大小限制的方法



max_execution_time=600

max_input_time=600

memory_limit=32m

file_uploads=on

upload_tmp_dir=/tmp

upload_max_filesize=32m

post_max_size=32m



