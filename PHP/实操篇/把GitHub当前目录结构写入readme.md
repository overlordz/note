```php
<?php

class scandDir
{
    public $url;
    public $ignoreDirArr;

    public function __construct($url, $ignoreDirArr = [])
    {
        $this->url = $url;
        $this->ignoreDirArr = $ignoreDirArr;
    }

    /**
     *
     * @param string $dir    文件夹的路径
     * @param string $strDir 返回字符串
     * @return bool|string
     * @author overlordz
     */
    public function scandDir(string $dir, string &$strDir = '')
    {
        if (is_dir($dir)) {
            if ($handle = opendir($dir)) {
                //返回当前文件的条目
                while (($file = readdir($handle)) !== false) {
                    $relativeDirectory = str_replace(__DIR__, '', $dir);
                    $countDirLevel = substr_count($relativeDirectory, DIRECTORY_SEPARATOR);
                    if (!in_array($file, $this->ignoreDirArr)) {
                        $subFullDirectory = $dir . DIRECTORY_SEPARATOR;
                        $strExt = str_repeat('    ', $countDirLevel) . '* ';
                        //判断子目录是否还存在子目录
                        if (is_dir($subFullDirectory . $file)) {
                            $strDir .= $strExt . $file . PHP_EOL;
                            $this->scandDir($subFullDirectory . $file, $strDir);
                        }
                        else {
                            $fileShow = '[' . $file . ']' . '('.$this->url . $relativeDirectory . DIRECTORY_SEPARATOR . $file . ')';
                            $strDir .= $strExt . $fileShow . PHP_EOL;
                        }
                    }
                }
                //关闭文件夹
                closedir($handle);
                return $strDir;
            }
        }
        return false;
    }
}

$dir = __DIR__;
$url = 'https://github.com/overlordz/note/blob/master';
$headTitle = '
## PHPer 工具书

本项目的所有资料来源于网上，再加上本人整理验证，其目的是为了可以当资料书进行翻阅和回顾。

如果本项目对你有用，麻烦点star，不要fork

对应的文章如果有描述错误，欢迎提交Issues。

### 目录：
';
$ignoreDir = [
    '.',
    '..',
    '.idea',
    '.git',
    'assets'
];
$scandDirClass = new scandDir($url,$ignoreDir);
$scandDirArr = $scandDirClass->scandDir($dir);
file_put_contents(__DIR__ . '/readme.md', $headTitle . $scandDirArr);
echo '目录结构更新成功'.PHP_EOL;

```

