在实际应用中经常需要导出大数据到 **Excel** 表中，而常用的 `PHPexcel` 包需要把所有数据拿到后才能生成 excel， 在面对生成超大数据量的 Excel 文件时这显然是会造成内存溢出的，所以考虑使用让 **PHP** 边写入输出流边让浏览器下载的形式来完成需求。



`php://output` 是一个可写的输出流，允许程序像操作文件一样将输出写入到输出流中，PHP 会把输出流中的内容发送给 web 服务器并返回给发起请求的浏览器

另外由于 excel 数据是从数据库里逐步读出然后写入输出流的所以需要将 PHP 的执行时间设长一点（默认 30 秒）`set_time_limit(0)` 不对 PHP 执行时间做限制。

**注：以下代码只是阐明生成大数据量 EXCEL 的思路和步骤，请根据自己的需求填充对应的业务代码！**

```php
class CSVexport
{
      /**
     * 导出CSV文件
     * @param array $data        数据
     * @param array $header_data 首行数据
     * @param string $file_name  文件名称
     * @return string
     */
    public function csv_export($data = array(), $headlist = array(), $fileName) {
        header('Content-Type: application/vnd.ms-excel');
        header('Content-Disposition: attachment;filename="'.$fileName.'.csv"');
        header('Cache-Control: max-age=0');
        header("Expires: 0");
        //打开PHP文件句柄,php://output 表示直接输出到浏览器
        $fp = fopen('php://output', 'a');
        
        //输出Excel列名信息
        if ($headlist) {
            foreach ($headlist as $key => $value) {
                //CSV的Excel支持GBK编码，一定要转换，否则乱码
                $headlist[$key] = iconv('utf-8', 'gbk', $value);
            }
            //将数据通过fputcsv写到文件句柄
            $fresult = fputcsv($fp, $headlist);
        }
        //每隔$limit行，刷新一下输出buffer，不要太大，也不要太小
        $limit = 100000;

        //逐行取出数据，不浪费内存
        //$count = count($data);
        foreach ($data as $k => $v) {
            //必须同时使用 ob_flush() 和flush() 函数来刷新输出缓冲，防止由于数据过多造成问题
            if( $k % $limit == 0 && $k!=0 ) { 
                ob_flush();
                flush();
            }
            $row = $v;
            foreach ($row as $key => $value) {
                $value = str_replace('"', '""', "\t".$value);
                $row[$key] = iconv('utf-8', 'gbk', $value);
            }
            fputcsv($fp, $row);
        }
        fclose($fp);
    }

}


$fileName = "下载文件名称";
$excelHeader = ["首栏标题显示名称"];
// 根据自己数据库进行拿取
$manyData = [
    [
        'key' => '假设许多数据'
    ]
];
$csv = new CSVexport();
$data = [];
foreach ($manyData as $k => $val) {
    $data[$k][] = $val['key'];
}
$csv->csv_export($data, $excelHeader, $fileName);
```



好了， 其实很简单，就是用逐步写入输出流并发送到浏览器让浏览器去逐步下载整个文件，由于是逐步写入的无法获取文件的总体 size 所以就没办法通过设置`header("Content-Length: $size");` 在下载前告诉浏览器这个文件有多大了。不过不影响整体的效果这里的核心问题是解决大文件的实时生成和下载。