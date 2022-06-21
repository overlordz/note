#### 创建用户
> 命令 : CREATE USER 'username'@'host' IDENTIFIED BY 'password'; 

> 说明:username - 你将创建的用户名, host - 指定该用户在哪个主机上可以登陆,如果是本地用户可用localhost, 如果想让该用户可以从任意远程主机登陆,可以使用通配符%. password - 该用户的登陆密码,密码可以为空,如果为空则该用户可以不需要密码登陆服务器. 

```
例子：
    CREATE USER 'dog'@'localhost' IDENTIFIED BY '123456'; 
    CREATE USER 'pig'@'192.168.1.101_' IDENDIFIED BY '123456'; 
    CREATE USER 'pig'@'%' IDENTIFIED BY '123456'; 
    CREATE USER 'pig'@'%' IDENTIFIED BY ''; 
    CREATE USER 'pig'@'%'; 
```

#### 授权
> 命令:GRANT privileges ON databasename.tablename TO 'username'@'host' 

> 说明: privileges - 用户的操作权限,如SELECT , INSERT , UPDATE 等(详细列表见该文最后面).如果要授予所的权限则使用ALL.;databasename - 数据库名,tablename-表名,如果要授予该用户对所有数据库和表的相应操作权限则可用*表示, 如*.*. 

```
例子:
    赋予所有权限：
                grant all privileges on *.* TO 'new_username'@'%' with grant option;
    
    赋予相对应的操作：
    GRANT SELECT, INSERT,update,delete ON mydb.TestTable TO 'pig'@'%'; 
    
    注意:用以上命令授权的用户不能给其它用户授权,如果想让该用户可以授权,用以下命令: 
    GRANT privileges ON databasename.tablename TO 'username'@'host' WITH GRANT OPTION; 
    
    提交并立即生效
        commit;
        flush privileges;
```

#### 设置与更改用户密码 

> 命令 : SET PASSWORD FOR 'username'@'host' = PASSWORD('newpassword');如果是当前登陆用户用SET PASSWORD = PASSWORD("newpassword"); 

```

例子: SET PASSWORD FOR 'pig'@'%' = PASSWORD("123456"); 

```

#### 撤销用户权限 

> 命令: REVOKE privilege ON databasename.tablename FROM 'username'@'host'; 

> 说明: privilege, databasename, tablename - 同授权部分. 

```

例子: REVOKE SELECT ON *.* FROM 'pig'@'%'; 

```

#### 删除用户 

> 命令: DROP USER 'username'@'host'; 





#### 实操记录

> mariadb

```
创建 xiaohu 账号，并设置默认密码
CREATE USER 'xiaohu'@'%' IDENTIFIED BY '123456';

赋权所有权限
grant all privileges on *.* TO 'xiaohu'@'%' with grant option;
commit;
flush privileges;

创建用户并授权指定数据库
CREATE USER 'xiaohu'@'%' IDENTIFIED BY 'Xiaohu123456'; 
GRANT SELECT,
	INSERT,
	UPDATE,
	REFERENCES,
	DELETE,
	CREATE,
	DROP,
	ALTER,
	INDEX,
	TRIGGER,
	CREATE VIEW,
	SHOW VIEW,
	EXECUTE,
	ALTER ROUTINE,
	CREATE ROUTINE,
	CREATE TEMPORARY TABLES,
	LOCK TABLES,
	EVENT ON `yinnong`.* TO 'xiaohu'@'%';
GRANT GRANT OPTION ON `dataName`.* TO 'xiaohu'@'%';
flush privileges;
```



