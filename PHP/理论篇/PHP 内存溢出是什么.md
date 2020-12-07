> 原文地址 [mp.weixin.qq.com](https://mp.weixin.qq.com/s?__biz=MzIxMDA0OTcxNA==&mid=2654261340&idx=1&sn=ee028bd1102f35f4363eb13314ab6a3c&chksm=8caaff81bbdd769707de4fc09bae24cb7a7c6f95483b7e3adedcfe53942fca79b1d6c8de9a3d&scene=126&sessionid=1606267442&key=5b94f92116022b027a0cc5279e0999300fe8bfb2dae29e7690fb05673cc8472ef88289a582ac0777a366187ed58f750c1be97aa885d774bf92ecec138e70854afb2d9864d38ba7117ae09fa8952b17074071c5be4777c2b8c139945548cb6cfe042a102cbe0fa3ce0ef076dc9b9b7dacb84fc2411f91d1d0287c329ea63df64a&ascene=1&uin=MTcxNDYxMjQ2Mg%3D%3D&devicetype=Windows+10+x64&version=63000039&lang=zh_CN&exportkey=A%2FuE0AH0ydTpZ4Z98tl%2BVd8%3D&pass_ticket=Wqnnh3Ja2mdturcM5LyIrmS6w6ZI1Wsqjy3YOb8t6uYg3a67ckT%2BJufjefVrxh%2BV&wx_header=0)

**什么是内存溢出**  

内存溢出是指应用系统中存在无法回收的内存或使用的内存过多，最终使得程序运行要用到的内存大于虚拟机能提供的最大内存。

**引起内存溢出的原因有很多种，常见的有以下几种：**  
1 内存中加载的数据量过于庞大，如一次从数据库取出过多数据；  
2 集合类中有对对象的引用，使用完后未清空；  
3 代码中存在死循环或循环产生过多重复的对象实体；  
4 使用的第三方软件中的 BUG；  
5 启动参数内存值设定的过小；

**下面来看些在开发中经常遇到的内存溢出的实例**

内存溢出经常报错的情况  

```
PHP Fatal error: Allowed memory size of 268 435 456 bytes exhausted


```

**1 处理数组时出现内存溢出**  
1）使用迭代生成器，可以通过继承 Iterator 接口实现  
2）使用关键词 yield  

```
function xrange($start, $end, $step = 1) {
    for ($i = $start; $i <= $end; $i += $step) {
        yield $i;
    }
}

foreach (xrange(1, 1000000) as $num) {
    echo $num, "\n";
}


```

**2. 使用 sql 查询数据，查出来很多，导致内存溢出**  
sql 语句在 mysql 中可以查询，但是使用 php 程序查询就报 php 内存溢出  
1）这个问题在 php 的官方网站叫缓冲查询和非缓冲查询。php 的查询缺省模式是缓冲模式。也就是，查询数据结果一次全部提取到内存里供 php 程序额外的功能，比如说，计算行数，将指针指向某一行等。

更重要的是程序对数据集反复进行二次查询和过滤操作。但这种缓冲查询模式的缺陷是消耗内存，也就是用空间换速度。


2）另外一种查询模式是非缓冲查询，数据库服务器会一条一条的返回数据，而不是一次全部返回，这样的结果是 php 程序消耗较少的内存，但却增加了数据库服务器的压力，因为数据库会一直等待 php 来取数据，一直到数据全部取完。  

1. 首先查询数据库需要进行 limit 进行分页查询  
2. 如果不使用 limit，使用非缓冲查询

```
1.mysql:
$conn = mysql_connect("localhost", "user", "pass");
$db   = mysql_select_db("world");
$uresult = mysql_unbuffered_query("SELECT Name FROM City");    //非缓冲查询
if ($uresult) {
   while ($row = mysql_fetch_assoc($uresult)) {
       echo $row['Name'] . PHP_EOL;
   }
}

2.pdo_mysql:
$pdo = new PDO("mysql:host=localhost;db, 'my_user', 'my_pass');
$pdo->setAttribute(PDO::MYSQL_ATTR_USE_BUFFERED_QUERY, false);    //设置这个属性，就为非缓冲查询
$uresult = $pdo->query("SELECT Name FROM City");
if ($uresult) {
   while ($row = $uresult->fetch(PDO::FETCH_ASSOC)) {
       echo $row['Name'] . PHP_EOL;
   }
}

3.mysqli:
$mysqli  = new mysqli("localhost", "user", "password", "world");
$uresult = $mysqli->query("SELECT Name FROM City", MYSQLI_USE_RESULT);
if ($uresult) {
   while ($row = $uresult->fetch_assoc()) {
       echo $row['Name'] . PHP_EOL;
   }
}
$uresult->close();


```

**3 假定日志中存放的记录数为 500000 条，那么解决方案如下：**

ini_set(‘memory_limit’,’64M’);　// 重置 php 可以使用的内存大小为 64M，一般在远程主机上是不能修改 php.ini 文件的，只能通过程序设置。

注：在 safe_mode（安全模式）下，ini_set 失效

```
set_time_limit(600);//设置超时限制为６分钟
$farr = $Uarr = $Marr = $IParr = $data = $_sub = array();
$spt = ”$@#!$”;
$root = ”/Data/webapps/VisitLog”;
$path = $dpath = $fpath = NULL;
$path = $root.”/”.date(“Y-m”,$timestamp);
$dpath = $path.”/”.date(“m-d”,$timestamp);

for($j=0;$j<24;$j++){
    $v = ($j < 10) ? ”0″.$j : $j;
    $gpath = $dpath.”/”.$v.”.php”;

    if(!file_exists($gpath)){
        continue;

    } else {
        $arr = file($gpath);////将文件读入数组中
        array_shift($arr);//移出第一个单元－》
        $farr = array_merge($farr,$arr);
        unset($arr);
    }
}

if(empty($this->farr)){
    echo ”没有相关记录！”;
    exit;
}

while(!empty($farr)){
    $_sub = array_splice($farr, 0, 10000); //每次取出$farr中1000个
    for($i=0,$scount=count($_sub);$i<$scount;$i++){
        $arr = explode($spt,$_sub[$i]);
        $Uarr[] = $arr[1]; //vurl
        $Marr[] = $arr[2]; //vmark
        $IParr[] = $arr[3].” |$nbsp;”.$arr[1]; //IP
    }
    unset($_sub);//用完及时销毁
}
unset($farr);


```

这里，不难看出，一方面，我们要增加 PHP 可用内存大小，另一方面，只要我们想办法对数组进行分批处理，分而治之，将用过的变量及时销毁 (unset)，一般是不会出现溢出问题的。

**4 上传 excel 文件时，出现内存溢出的情况**  
在文件中分配大点的内存设置内存治标不治本，而且服务器的 php.ini（memory_limit =128M）有时候是很难改的。所以在文件中设置。但是只有 php.ini 中的安全模式 safe_mode 开启时才可以设置  

```
ini_set('memory_limit', '521M');


```

解决方法：

```
protected /extensions/ExcelHelper.php 中945行 
$PHPReader->setReadDataOnly(true);  //在拿到数据后进行设置只读

    public static function importFromExcel($filePath, $blankRowDel = false)
    {
        set_time_limit(90);
        Yii::import('application.extensions.phpexcel.PHPExcel');
        $PHPExcel = new PHPExcel();
        //默认用excel2007读取excel，若格式不对，则用之前的版本进行读取
        $PHPReader = new PHPExcel_Reader_Excel2007();
        if (!$PHPReader->canRead($filePath)) {
            $PHPReader = new PHPExcel_Reader_Excel5();
            if (!$PHPReader->canRead($filePath)) {
                throw new Exception('can not read the excel file');
            }
        }
        $PHPReader->setReadDataOnly(true);

        $PHPExcel      = $PHPReader->load($filePath);
        $allSheetCount = $PHPExcel->getSheetCount();
        $excelData     = array();
        for ($currentSheetNum = 0; $currentSheetNum < $allSheetCount; $currentSheetNum++) {
            //读取excel文件中的第一个工作表
            $currentSheet = $PHPExcel->getSheet($currentSheetNum);
            //取得当前表名
            $currentSheetTitle = $currentSheet->getTitle();
            //取得最大的列号
            $allColumn = $currentSheet->getHighestColumn();
            //取得一共有多少行
            $allRow = $currentSheet->getHighestRow();
            // 从第一行获取列名
            $currentRow = 1;
            // 从第A列开始输出
            $colunmNameArray = array();
            $max_column_num  = PHPExcel_Cell::columnIndexFromString($allColumn);
            for ($current_column_num = 0; $current_column_num <= $max_column_num; $current_column_num++) {
                $currentColumn = PHPExcel_Cell::stringFromColumnIndex($current_column_num);
                $val           = $currentSheet->getCellByColumnAndRow($current_column_num, $currentRow)->getValue();
                if (empty($val)) {
                    continue;
                }
                if (is_object($val)) {
                    $colunmNameArray[$currentColumn] = '';
                    foreach ($val->getRichTextElements() as $cell) {
                        $colunmNameArray[$currentColumn] .= $cell->getText();
                    }
                } else {
                    $colunmNameArray[$currentColumn] = $val;
                }
            }

            //从第二行开始输出，因为excel表中第一行为列名
            $sheetData = array();
            for ($currentRow = 2; $currentRow <= $allRow; $currentRow++) {
                //从第A列开始输出 */
                $rowData   = array();
                $blankCell = 0;
                for ($current_column_num = 0; $current_column_num <= $max_column_num; $current_column_num++) {
                    $currentColumn = PHPExcel_Cell::stringFromColumnIndex($current_column_num);
                    $val           = $currentSheet->getCellByColumnAndRow($current_column_num, $currentRow)->getValue();
                    if (!isset($colunmNameArray[$currentColumn])) {
                        continue;
                    }
                    //如果输出汉字有乱码，则需将输出内容用iconv函数进行编码转换，如下将gb2312编码转为utf-8编码输出
                    if (is_object($val)) {
                        $rowData[$currentColumn] = '';
                        foreach ($val->getRichTextElements() as $cell) {
                            $rowData[$currentColumn] .= $cell->getText();
                        }
                    } else {
                        $rowData[$currentColumn] = $val;
                    }

                    if (empty($rowData[$currentColumn])) {
                        $blankCell++;
                    }
                }

                if (!$blankRowDel || chr($blankCell + 64) != $allColumn) {
                    $sheetData[] = $rowData;
                }
            }

            $excelData[$currentSheetTitle] = array(
                'header'  => $colunmNameArray,
                'content' => $sheetData,
            );
        }
        return $excelData;
    }


```
