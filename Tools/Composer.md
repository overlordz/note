# Composer


## 安装

**windows**

点击链接 `https://getcomposer.org/Composer-Setup.exe` 下载，直接安装就可以了。

**Mac/Linux**

```
$ php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
$ php -r "if (hash_file('SHA384', 'composer-setup.php') === '544e09ee996cdf60ece3804abc52599c22b1f40f4323403c44d44fdfdd586475ca9813a858088ffbc1f233e9b180f061') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
$ php composer-setup.php
$ php -r "unlink('composer-setup.php');"
```

## 软件仓库

使用composer安装软件时是从远程服务器下载的，但国内用户访问国外网站会过慢或不能访问。有些机构将国外的软件同步到了国内服务器。我们将composer下载的服务器指向到这个国内服务器，就可以实现快速下载软件了。

### 国内仓库

参考网址 `https://pkg.phpcomposer.com/` ，命令行执行以下指令完成配置(linux需要使用sudo)

```
composer config -g repo.packagist composer https://packagist.phpcomposer.com
```

### 国外仓库

如果国内镜像有问题通过以下方式切换为国外源

1. 修改项目 `composer.json` 文件

```
...
"repositories": {
    "packagist": {
        "type": "composer",
        "url": "https://packagist.org"
    }
}
...
```

2. 全局更改

```
composer config -g repo.packagist composer https://packagist.org
```

## 安装扩展包

PHP 扩展软件包应用商店网址 `https://packagist.org/` ，开发中通过在应用商店中搜索需要的软件包进行安装就可以了。

执行以下命令安装 `https://packagist.org/packages/overlordz/note` 软件包，其他软件包安装方式都是一样操作。安装后参考软件包的 `Github` 软件包操作的 `Readme.md` 文件或提供的网站使用就可以了。

```
composer require overlordz/note
```

## 本地扩展

开发一个本地包的标准流程是  `开发>git->packagist>使用`  但是我们在进行本地开发时，尤其是为某些产品开发组件时，比如laravel框架等。这样的操作方式非常不方便，我们希望直接在本地开发，并可以正常使用 `composer` 功能。

### 创建项目

首先远行 `composer init` 创建composer.json配置文件。

比如我们的文件目录在 `~/code/overlordz/note`

```
.
├── composer.json
├── src
│   └── Hd.php
└── tests
```

主要是 composer.json 配置文件，内容如下：

```
{
  "name": "overlordz/note",
  "description": "study note",
  "license": "MIT",
  "authors": [
    {
      "name": "overlordz",
      "email": "12345678@qq.com"
    }
  ],
  "autoload": {
    "psr-4": {
      "OverLordz\\arr\\": "src/"
    }
  }
}
```

> 根据自己的需要自行修改文件内容，比如 `require` 其他的包

### 配置扩展包

在 `hdphp` 或 `laravel` 其实是任何composer项目的根目录下执行： 

```
composer config repositories.arr path ~/code/overlordz/note
```

这样就将包 `arr` 软链接到 `~/code/overlordz/note` 。修改 ``~/code/overlordz/note`` 目录下的文件，将影响到 `vendor/overlordz/note`。

这里观察 `laravel` 等 composer 项目根目录下的 `composer.json` 会多一段内容：

```
...
"repositories": {
	"arr": {
		"type": "path",
		"url": "/Users/code/overlordz/note"
	}
}
...
```

> 表示使用我们本地的 composer 包，而非 packagist 下载的包

### 安装本地包

安装本地包与远程包没有什么区别。

```
composer require overlordz/note
```

至些我们已经完成了，直接在 overlordz/note 目录中开发就可以了，而非 `vendor/overlordz/module` 目录。



### 常用命令

```bash
# 安装包,根据composer.json
composer install;
# 更新包,升级composer.json的所有代码库(如果能升级的话)
composer update;
# 搜索包,搜索composer可用的包
composer search 关键字;
# 引入包,会在composer.json新增一条包配置,并下载该代码包 
composer require 包名称;
# 删除包
composer remove 包名称;
# 生成当前命名空间与类库文件路径的一个映射，运行时加载会直接读取这个映射，加快文件的加载速度。
composer dump-autoload;
# 更新命名空间映射
composer dumpautoload
```

