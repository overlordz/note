

国内一些网站系统默认应该都不是**utf-8**的编码，大多数还是**gbk**或者是**gb2312**，可能是由于历史原因，这个就不展开。



解决代码逻辑如下

```php
$content = '';
$text = file_get_contents($file);

// mb_detect_encoding 检测字符的编码 有一些bug
//$encodType = mb_detect_encoding($text);
define('UTF32_BIG_ENDIAN_BOM', chr(0x00) . chr(0x00) . chr(0xFE) . chr(0xFF));
define('UTF32_LITTLE_ENDIAN_BOM', chr(0xFF) . chr(0xFE) . chr(0x00) . chr(0x00));
define('UTF16_BIG_ENDIAN_BOM', chr(0xFE) . chr(0xFF));
define('UTF16_LITTLE_ENDIAN_BOM', chr(0xFF) . chr(0xFE));
define('UTF8_BOM', chr(0xEF) . chr(0xBB) . chr(0xBF));
$first2 = substr($text, 0, 2);
$first3 = substr($text, 0, 3);
$first4 = substr($text, 0, 3);
$encodType = "";
if (UTF8_BOM == $first3) {
    $encodType = 'UTF-8 BOM';
} else if (UTF32_BIG_ENDIAN_BOM == $first4) {
    $encodType = 'UTF-32BE';
} else if (UTF32_LITTLE_ENDIAN_BOM == $first4) {
    $encodType = 'UTF-32LE';
} else if (UTF16_BIG_ENDIAN_BOM == $first2) {
    $encodType = 'UTF-16BE';
} else if (UTF16_LITTLE_ENDIAN_BOM == $first2) {
    $encodType = 'UTF-16LE';
}

//下面的判断主要还是判断ANSI编码的·
if ('' == $encodType) {
    //即默认创建的txt文本-ANSI编码的
    $content = iconv("GBK", "UTF-8", $text);
} else if ('UTF-8 BOM' == $encodType) {
    //本来就是UTF-8不用转换
    $content = $text;
} else {
    //其他的格式都转化为UTF-8就可以了
    $content = iconv($encodType, "UTF-8", $text);
}

```

