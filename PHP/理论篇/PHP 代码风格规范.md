# php代码风格规范


本规范融合了 [PSR-1][]（[中文][PSR-1-CN]） 和 [PSR-2][]（[中文][PSR-2-CN]），根据现有项目的状态制定。

本规范希望通过制定一系列规范化 PHP 代码的规则，以减少在浏览不同作者的代码时，因代码风格的不同而造成不便。

当多名工程师在多个项目中合作时，就需要一个共同的编码规范，
而本文中的风格规范源自于多个不同项目代码风格的共同特性，
因此，本规范的价值在于我们都遵循这个编码风格，而不是在于它本身。

关键词 “必须”("MUST")、“一定不可/一定不能”("MUST NOT")、“需要”("REQUIRED")、
“将会”("SHALL")、“不会”("SHALL NOT")、“应该”("SHOULD")、“不该”("SHOULD NOT")、
“推荐”("RECOMMENDED")、“可以”("MAY")和”可选“("OPTIONAL")的详细描述可参见 [RFC 2119][]。

[PSR-1]: http://www.php-fig.org/psr/psr-1/
[PSR-2]: http://www.php-fig.org/psr/psr-2/
[PSR-4]: http://www.php-fig.org/psr/psr-4/
[PSR-1-CN]: https://github.com/PizzaLiu/PHP-FIG/blob/master/PSR-1-basic-coding-standard-cn.md
[PSR-2-CN]: https://github.com/PizzaLiu/PHP-FIG/blob/master/PSR-2-coding-style-guide-cn.md
[RFC 2119]: https://www.ietf.org/rfc/rfc2119.txt

<a id="s0"></a>

## 目录

1. [概览](#s1)
2. [文件](#s2)
3. [空格](#s3)
4. [类的常量、属性和方法](#s4)
5. [控制结构](#s5)
6. [闭包](#s6)
7. [代码太长时的折行方法](#s7)
8. [代码按列对齐](#s8)
9. [代码注释和文档](#s9)
10. [最佳实践](#s10)
11. [总结](#s11)

<a id="s1"></a>
## 1. 概览 [^](#s0)

- PHP代码文件**必须**以 `<?php` 或 `<?=` 标签开始。

- PHP代码文件**必须**以 `不带 BOM 的 UTF-8` 编码。

- PHP代码文件**必须**以 `LF(\n)` 作为换行符。

- 代码**必须**使用 4 个空格而不是 Tab 键进行缩进。

- 每行的字符数**应该**保持在 80 个之内，理论上**一定不可**多于 100 个，超过限制的折成多行。

- PHP代码中**应该**只定义类、函数、常量等声明，或其他会产生 `从属效应` 的操作（如：生成文件输出以及修改.ini配置文件等），二者只能选其一。

- 类的命名**必须**遵循 `StudlyCaps` 大写开头的驼峰或用下划线 `_` 连接小写单词的命名规范。

- 方法名称**必须**符合 `camelCase` 式的小写开头驼峰或用下划线 `_` 连接小写单词的命名规范。

- 类中的常量**必须**用下划线 `_` 连接**大写**单词的命名规范。

- 类中的属性**必须**用下划线 `_` 连接**小写**单词的命名规范。

- 函数名称**必须**用下划线 `_` 连接**小写**单词的命名规范。

- 全局常量**必须**用下划线 `_` 连接**大写**单词的命名规范。

- 变量名称**必须**用下划线 `_` 连接**小写**单词的命名规范。

- 每个 `namespace` 命名空间声明语句和 `use` 声明语句块后面，**必须**插入一个空白行。

- 类的开始花括号(`{`)**必须**写在函数声明后自成一行，结束花括号(`}`)也**必须**写在函数主体后自成一行。

- 方法的开始花括号(`{`)**必须**写在函数声明后自成一行，结束花括号(`}`)也**必须**写在函数主体后自成一行。

- 类的属性和方法**必须**添加访问修饰符（`private`、`protected` 以及 `public`）， `abstract` 以及 `final` **必须**声明在访问修饰符之前，而 `static` **必须**声明在访问修饰符之后。

- 类的属性和方法名**不该**以下划线（_）作为前缀，如 `_getTime`。

- 控制结构的关键字后**必须**要有一个空格，而调用方法或函数时则**一定不能**有。

- 控制结构的开始花括号(`{`)**必须**写在声明后自成一行，不能跟在声明后面，而结束花括号(`}`)**必须**写在主体后自成一行。

- 控制结构的开始左括号后和结束右括号前，都**一定不能**有空格。

- [关键字][] 以及 `true` `false` `null` **必须**全部小写。

[关键字]: http://php.net/manual/en/reserved.keywords.php



#### 1.1. 例子

以下例子程序简单地展示了以上大部分规范：

```php
namespace Vendor\Package;

use FooInterface;
use BarClass as Bar;
use OtherVendor\OtherPackage\BazClass;

class Foo extends Bar implements FooInterface
{
    const VERSION = '1.0';

    const DATE_APPROVED = '2012-06-01';

    public function sampleFunction($user_id, $b = null)
    {
        if ($user_id === $b)
        {
            bar();
        }
        else if ($user_id > $b)
        {
            $foo->bar($arg1);
        }
        else
        {
            BazClass::bar($arg2, $arg3);
        }
    }

    final public static function bar()
    {
        // method body
    }
}
```
或

```php
class mod_group extends model implements db_interface
{
    const VERSION = '1.0';

    const DATE_APPROVED = '2012-06-01';

    public function sample_function($user_id, $b = null)
    {
        if ($user_id === $b)
        {
            bar();
        }
        else if ($user_id > $b)
        {
            $foo->bar($arg1);
        }
        else
        {
            baz_class::bar($arg2, $arg3);
        }
    }

    final public static function bar()
    {
        // method body
    }
}
```


<a id="s2"></a>
## 2. 文件 [^](#s0)

#### 2.1. 通则

- 每个类都独立为一个文件。

- 多个类方法或函数定义用一个空行分隔。

- 行尾**一定不能**有多余的空格。

- 空行**可以**使得阅读代码更加方便以及有助于代码的分块。

- 每行**一定不能**存在多于一条语句。

- 所有PHP文件**必须**使用 `Unix LF (linefeed)` 作为行的结束符。

- 所有PHP文件**必须**以一个空白行作为结束。

- 纯PHP代码文件**必须**省略最后的 `?>` 结束标签。


#### 2.2. PHP标签

PHP 代码**必须**使用 `<?php ?>` 长标签 或 `<?= ?>` 短输出标签，**一定不可**使用其它自定义标签。

#### 2.3. 字符编码

PHP 代码**必须**且只可使用`不带 BOM 的 UTF-8` 编码。

#### 2.4. 从属效应（副作用）

一份 PHP 文件中**应该**要不就只定义新的声明，如类、函数或常量等不产生从属效应的操作，
要不就只有会产生从属效应的逻辑操作，但**不该**同时具有两者。

“从属效应”（side effects）一词的意思是，仅仅通过包含文件，不直接声明类、函数和常量等，而执行的逻辑操作。

“从属效应”包含却不仅限于：生成输出、直接的 `require` 或 `include`、
连接外部服务、修改 ini 配置、抛出错误或异常、修改全局或静态变量、读或写文件等。

以下是一个反例，一份包含声明以及产生从属效应的代码：

```php
// 从属效应：修改 ini 配置
ini_set('error_reporting', E_ALL);

// 从属效应：引入文件
include "file.php";

// 从属效应：生成输出
echo "<html>\n";

// 声明函数
function foo()
{
    // 函数主体部分
}
```

下面是一个范例，一份只包含声明不产生从属效应的代码：

```php
// 声明函数
function foo()
{
    // 函数主体部分
}

// 条件声明**不**属于从属效应
if (! function_exists('bar'))
{
    function bar()
    {
        // 函数主体部分
    }
}
```

<a id="s3"></a>
## 3. 空格 [^](#s0)

- 控制结构的关键字后**必须**要有一个空格，而调用方法或函数时方法和函数名后**一定不能**有。

- 代码注释符 `//` `/* */` 与注释内容之间**必须**有一个空格。如果在行尾注释，注释符与代码之间必须有一个空格。

- 函数和方法参数列表中，每个逗号后面**必须**要有一个空格，而逗号前面**一定不能**有空格。

- 数组成员列表中，每个逗号后面**必须**要有一个空格，而逗号前面**一定不能**有空格。

- 方法和函数调用的开始左括号后和结束右括号前，**一定不能**有空格。

- 控制结构的开始左括号后和结束右括号前，**一定不能**有空格。

- 赋值操作符 `=` `=>` `.=` `+=` 等两边**必须**有一个空格。为了保持列对齐，左边的空格数可以超过一个，右边**必须**只有一个空格。

- 字符串连接操作符 `.` 双边**必须**有空格。

- 类型转换操作右括号后面不能有空格， 例如：`(int)$uid`。

- 类方法调用操作符 `->` 两边**必须**没有空格，除非位于行首。

- 数组成员操作符 `[` `]` 两边**必须**没有空格。

- 语句终止符 `;` 两边**必须**没有空格。

- 如下操作符 `!`（逻辑非）`&`（引用 ） `++` `--` `@`（屏蔽错误） `~`（位操作）`...` 右边**必须**不能有空格。

- 其他操作符 `+` `-` `*` `/` `%` `**` `==` `==` `!=` `!==` `>` `>=` `<` `<=` `&&` `||`
`as` `<<` `&` `instanceof` `?:` 等两边**必须**有且只有一个空格。

<a id="s4"></a>
## 4. 类的常量、属性和方法 [^](#s0)

此处的“类”指代所有的类、接口以及可复用代码块（traits）

#### 4.1. 属性

类的属性命名**必须**是下划线分隔式 (`_`)。

每个属性都**必须**添加访问修饰符。

**一定不可**使用关键字 `var` 声明一个属性。

每条语句**一定不可**定义超过一个属性。

**不要**使用下划线作为前缀来区分属性是 public，protected 或 private。

以下是属性声明的一个范例：

```php
namespace Vendor\Package;

/**
 * Class Description...
 */
class ClassName
{
    /**
     * @var int
     */
    public $foo = 0;
}
```

#### 4.2. 方法

方法名称**必须**符合 `camelCase()` 式的小写开头驼峰或用下划线 `_` 连接小写单词的命名规范，
不管选择哪一种，同一个项目中**必须**保持一致。

所有方法都**必须**添加访问修饰符。

**不要**使用下划线作为前缀，来区分方法是 public，protected 或 private。

方法名称后**一定不能**有空格，其开始花括号**必须**独占一行，结束花括号也**必须**在方法主体后单独成一行。

参数左括号后和右括号前**一定不能**有空格。

一个标准的方法声明可参照以下范例，留意其括号、逗号、空格以及花括号的位置。

```php
namespace Vendor\Package;

/**
 * Class Description...
 */
class ClassName
{
    /**
     * summary...
     *
     * @param callable $arg1 回调函数
     * @param string $arg2
     * @param User[] $arg3
     *
     * @throw Exception 远程调用失败
     *
     * @return false|int[]
     */
    public function fooBarBaz($arg1, &$arg2, array $arg3 = [])
    {
        // method body
    }
}
```

#### 4.3. 方法的参数

参数列表中，每个逗号后面**必须**要有一个空格，而逗号前面**一定不能**有空格。

有默认值的参数，**必须**放到参数列表的末尾。

数组类型和自定义类型的参数**必须**写明。

```php
namespace Vendor\Package;

/**
 * Class Description...
 */
class ClassName
{
    /**
     * summary...
     *
     * @param callable $arg1 回调函数
     * @param string $arg2
     * @param User[] $arg3
     *
     * @throw Exception 远程调用失败
     *
     * @return false|int[]
     */
    public function foo($arg1, User &$arg2, array $arg3 = [])
    {
        // method body
    }
}
```

参数列表**可以**分列成多行，这样，包括第一个参数在内的每个参数都**必须**单独成行。

拆分成多行的参数列表后，结束括号以及方法开始花括号**必须**写在独立的一行。

```php
namespace Vendor\Package;

/**
 * Class Description...
 */
class ClassName
{
    /**
     * summary...
     *
     * @param ClassTypeHint $arg1
     * @param string $arg2
     * @param User[] $arg3
     *
     * @throw Exception 远程调用失败
     *
     * @return bool
     */
    public function aVeryLongMethodName(
        ClassTypeHint $arg1,
        &$arg2,
        array $arg3 = []
    )
    {
        // method body
    }
}
```

#### 4.4. 方法及函数调用

方法及函数调用时，方法名或函数名与参数左括号之间**一定不能**有空格，
参数右括号前也 **一定不能**有空格。每个参数前**一定不能**有空格，但其后**必须**有一个空格。

```php
bar();
$foo->bar($arg1);
Foo::bar($arg2, $arg3);
```

参数**可以**分列成多行，此时包括第一个参数在内的每个参数都**必须**单独成行。

```php
$foo->bar(
    $long_argument,
    $longer_argument,
    $much_longer_argument
);
```

#### 4.5. namespace 以及 use 声明

`namespace` 声明后**必须**插入一个空白行。

所有 `use` **必须**在 `namespace` 后声明。

每条 `use` 声明语句**必须**只有一个 `use` 关键词。

`use` 声明语句块后**必须**要有一个空白行。

例如：

```php
namespace Vendor\Package;

use FooClass;
use BarClass as Bar;
use OtherVendor\OtherPackage\BazClass;

// ... additional PHP code ...
```

#### 4.6. 扩展与继承

关键词 `extends` 和 `implements`**必须**写在类名称的同一行。

类的开始花括号**必须**独占一行，结束花括号也**必须**在类主体后独占一行。

```php
namespace Vendor\Package;

use FooClass;
use BarClass as Bar;
use OtherVendor\OtherPackage\BazClass;

/**
 * Class Description....
 */
class ClassName extends ParentClass implements \ArrayAccess, \Countable
{
    // constants, properties, methods
}
```

`implements` 的继承列表也**可以**分成多行，这样的话，每个继承接口名称都**必须**分开独立成行，包括第一个。

```php
namespace Vendor\Package;

use FooClass;
use BarClass as Bar;
use OtherVendor\OtherPackage\BazClass;

class ClassName extends ParentClass implements
    \ArrayAccess,
    \Countable,
    \Serializable
{
    // constants, properties, methods
}
```


#### 4.7. `abstract` 、 `final` 、 以及 `static`

需要添加 `abstract` 或 `final` 声明时， **必须**写在访问修饰符前，而 `static` 则**必须**写在其后。

```php
namespace Vendor\Package;

/**
 * Abstract Class
 */
abstract class ClassName
{
    /**
     * @var string
     */
    protected static $foo;

    /**
     * Method zim
     */
    abstract protected function zim();

    /**
     * Method bar
     *
     * @return void
     */
    final public static function bar()
    {
        // method body
    }
}
```


<a id="s5"></a>
## 5. 控制结构 [^](#s0)

- 控制结构关键词后**必须**有一个空格。

- 左括号 `(` 后**一定不能**有空格。

- 右括号 `)` 前也**一定不能**有空格。

- 结构体主体**一定**要有一次缩进。

- 开始花括号 `{` **一定**在结构声明后单独成行。

- 结束花括号 `}` **一定**在结构体主体后单独成行。

- 每个结构体的主体都**必须**被包含在成对的花括号之中。


#### 5.1. `if`、`else if` 和 `else`

标准的 `if` 结构如下代码所示，留意括号、空格以及花括号的位置，注意 `else` 和 `else if` 都与前面的结束花括号不在同一行。

`else if` 也可以写成 `elseif`。

```php
if ($expr1)
{
    // if body
}
else if ($expr2)
{
    // else if body
}
else
{
    // else body;
}
```

**一定不能**写成下面这样：

```php
if ($expr1) unset($list['count']);
else
    unset($list);

while ($expr2) $i++;
```

#### 5.2. `switch` 和 `case`

标准的 `switch` 结构如下代码所示，留意括号、空格以及花括号的位置。
`case` 语句**必须**相对 `switch` 进行一次缩进，而 `break` 语句以及 `case` 内的其它语句都**必须**相对 `case` 进行一次缩进。
如果存在非空的 `case` 直穿语句，主体里必须有类似 `// no break` 的注释。

```php
switch ($expr)
{
    case 0:
        echo 'First case, with a break';
        break;
    case 1:
        echo 'Second case, which falls through';
        // no break
    case 2:
    case 3:
    case 4:
        echo 'Third case, return instead of break';
        return;
    default:
        echo 'Default case';
        break;
}
```


#### 5.3. `while` 和 `do while`

一个规范的 `while` 语句应该如下所示，注意其括号、空格以及花括号的位置。

```php
while ($expr)
{
    // structure body
}
```

标准的 `do while` 语句如下所示，同样的，注意其括号、空格以及花括号的位置。

```php
do
{
    // structure body;
}
while ($expr);
```

#### 5.4. `for`

标准的 `for` 语句如下所示，注意其括号、空格以及花括号的位置。

```php
for ($i = 0; $i < 10; $i++)
{
    // for body
}
```

#### 5.5. `foreach`

标准的 `foreach` 语句如下所示，注意其括号、空格以及花括号的位置。

```php
foreach ($iterable as $key => $value)
{
    // foreach body
}
```

#### 5.6. `try`, `catch`

标准的 `try catch` 语句如下所示，注意其括号、空格以及花括号的位置。

```php
try
{
    // try body
}
catch (FirstExceptionType $e)
{
    // catch body
}
catch (OtherExceptionType $e)
{
    // catch body
}
```

<a id="s6"></a>
## 6. 闭包 [^](#s0)

闭包声明时，关键词 `function` 后以及关键词 `use` 的前后都**必须**要有一个空格。

开始花括号**必须**写在声明的下一行，结束花括号**必须**紧跟主体结束的下一行。

参数列表和变量列表的左括号后以及右括号前，**必须不能**有空格。

参数和变量列表中，逗号前**必须不能**有空格，而逗号后**必须**要有空格。

闭包中有默认值的参数**必须**放到列表的后面。

标准的闭包声明语句如下所示，注意其括号、逗号、空格以及花括号的位置。

```php
$closure_with_args = function ($arg1, $arg2)
{
    // body
};

$closure_with_args_and_vars = function ($arg1, $arg2) use ($var1, $var2)
{
    // body
};
```

参数列表以及变量列表**可以**分成多行，这样，包括第一个在内的每个参数或变量都**必须**单独成行，
而列表的右括号与闭包的开始花括号**必须**放在独立的一行。

以下几个例子，包含了参数和变量列表被分成多行的多情况。

```php
$long_args_no_vars = function(
    $long_argument,
    $longer_argument,
    $much_longer_argument
)
{
   // body
};

$no_args_long_vars = function () use (
    $long_var1,
    $longer_var2,
    $much_longer_var3
)
{
   // body
};

$long_args_long_vars = function (
    $long_argument,
    $longer_argument,
    $much_longer_argument
) use (
    $long_var1,
    $longer_var2,
    $much_longer_var3
)
{
   // body
};

$long_args_short_vars = function (
    $long_argument,
    $longer_argument,
    $much_longer_argument
) use ($var1)
{
   // body
};

$short_args_long_vars = function ($arg) use (
    $long_var1,
    $longer_var2,
    $much_longer_var3
)
{
   // body
};
```

注意，闭包被直接用作函数或方法调用的参数时，以上规则仍然适用。

```php
$foo->bar(
    $arg1,
    function ($arg2) use ($var1)
    {
        // body
    },
    $arg3
);
```

<a id="s7"></a>
## 7. 代码太长时的折行方法 [^](#s0)

每行的字符数**应该**保持在 80 个之内，理论上**一定不可**多于 100 个，超过限制的折成多行。

注意括号、空格以及花括号的位置。

#### 7.1. `if` `else if` `while` 条件太长

条件太长时按判断逻辑分行，每行一个判断逻辑，逻辑操作符放在行尾，每个判断逻辑与第一个列对齐。

范例：

```php
if ($update_time > 0 &&
    is_string($user_name) &&
    ($is_admin || $level < 1000))
{
    // if body
}
else if ($update_time > (time() - 7 * 86400) ||
         $gender == 1)
{
    // else if body
}
else
{
    $time_limit = time() + 3 * 86400;
    while ($count < 100 &&
           $update_time < $time_limit &&
           !$is_admin)
    {
        // while body
    }
}
```

反例：

```php
if ($update_time > 0
    && is_string($user_name) &&
    && ($is_admin || $level < 1000))
{
    // if body
}
else if ($update_time > (time() - 7 * 86400) ||
    $gender == 1)
{
    // else if body
}
else
{
    $time_limit = time() + 3 * 86400;
    while ($count < 100 &&
           $update_time < $time_limit && !$is_admin)
    {
        // while body
    }
}
```

#### 7.2 函数或方法定义的参数太多时

参数列表**可以**分列成多行，这样，包括第一个参数在内的每个参数都**必须**单独成行，比访问修饰符或 `function` 多一次缩进。

范例：

```php
/**
 * User Model
 */
class User
{
    /**
     * 获取多个用户的信息
     *
     * @param int $gid
     * @param int[] $uids
     * @param string[] $fields 需要查询的字段
     * @param bool $use_cache 是否使用缓存
     *
     * @return mixed[]
     */
    public function getMulti(
        $gid,
        array $uids,
        array $fields = [],
        $use_cache = true
    )
    {
        // method body
    }
}
```

反例：

```php
/**
 * User Model
 */
class User
{
    /**
     * 获取多个用户的信息
     *
     * @param int $gid
     * @param int[] $uids
     * @param string[] $fields 需要查询的字段
     * @param bool $use_cache 是否使用缓存
     *
     * @return mixed[]
     */
    public function getMulti($gid, array $uids,
        array $fields = [],
        $use_cache = true)
    {
        // method body
    }
}
```

#### 7.3 调用函数或方法的参数太多时

参数列表**可以**分列成多行，这样，包括第一个参数在内的每个参数都**必须**单独成行，比函数名多一次缩进。

范例：

```php
function foo()
{
    $gid = 123;

    $user = new User();
    $list = $user->getMultiFromDB(
        $gid,
        [11, 22, 33, 44, 55, 66, 77, 88]
        ['user_id', 'user_name', 'level', 'face']
    );
}
```

反例：

```php
function foo()
{
    $gid = 123;

    $user = new User();
    $list = $user->getMultiFromDB($gid,
        [11, 22, 33, 44, 55, 66, 77, 88]
        ['user_id', 'user_name', 'level', 'face']);
}
```

#### 7.4. 方法链式调用对齐

当方法链式调用需要折行时，`->` 需要列对齐。

范例：

```php
function foo()
{
    $group = new Group(123);
    $list = $group->setName('banana', true)
                  ->updateListCache()
                  ->getAll()
                  ->formatList();
}
```

反例：

```php
function foo()
{
    $group = new Group(123);
    $list = $group->setName('banana', true)->updateListCache()
                                           ->getAll()
                                           ->formatList();
}

function bar()
{
    $group = new Group(123);
    $list = $group->setName('banana', true)
        ->updateListCache()
        ->getAll()
        ->formatList();
}
```

#### 7.5. 三元操作

范例：

```php
function foo()
{
    $title = isset($row['data']['title']) ?
        $row['data']['title'] :
        $row['data']['content'];
}
```

反例：

```php
function foo()
{
    $title = isset($row['data']['title']) ?
        $row['data']['title'] : $row['data']['content'];
}
```

#### 7.6. 字符串太长

范例：

```php
$sql = "SELECT `user_id`, `user_name`, `age` " .
       "FROM `users` " .
       "WHERE `user_id` != 131212 AND `age` >= 18 " .
       "ORDER BY `age`";
```

反例：

```php
$sql = "SELECT `user_id`, `user_name`, `age` " .
   "FROM `users` " .
   "WHERE `user_id` != 131212 AND `age` >= 18 " .
   "ORDER BY `age`";
```

```php
$sql = "SELECT `user_id`, `user_name`, `age`
       FROM `users`
       WHERE `user_id` != 131212 AND `age` >= 18
       ORDER BY `age`";
```

<a id="s8"></a>
## 8. 代码按列对齐 [^](#s0)

#### 8.1. 变量定义

多个连续的局部变量定义可以按 `=` 列对齐，但变量名长度差别太大时不该对齐。

范例：

```php
$user_name = 'John';
$type      = 34;
$list[3]   = [1, 2, 3];
```

反例：

```php
$uid                       = 123;
$this_is_a_long_variable   = 32;
$result['list'][0]['type'] = 3;
```

#### 8.2. 数组定义

- **推荐**使用 `[]` 而不是 `array()` 定义数组。

- 数组定义**必须**一次定义完整，不允许使用多条赋值语句定义一个数组。

- 超过两个成员的键值数组定义，**必须**每行定义一对键值。

- 键值数组的赋值操作符**应该**按列对齐。

- 最后一个成员**必须**以逗号结束。

- 如果多个成员写在同一行，分隔成员的逗号之后**必须**有且只有一个空格。

- 只有值的数组，在成员很多时**可以**定义成多行，每行多个成员。


范例：

```php
$count = 32;

$result = [
    'count' => $count,
    'list'  => [
        [
            'user_id' => 123,
            'type'    => 88,
        ],
        [
            'user_id' => 124,
            'type'    => 89,
        ],
    ],
];

$types = ['123', 4555, 'abc'];

$uids = [
    123, 456, 789,
    1123, 1456, 1789,
    2123, 2456, 2789,
];

$map = ['a' => 3, 'b' => 4];
```

反例：

```php
$count = 32;

$result = [];
$result['count'] = $count;
$result['list'] = [
    [
        'user_id' => 123,
    ]
];

$map = ['a' => 3, 'b' => 5, 'x' => 32, 'y' => 'blah...', 'z' => 'long, long ago'];
```

<a id="s9"></a>
## 9. 代码注释和文档 [^](#s0)

- 只允许使用 `//` `/* */` 作为注释符，注释符与注释内容之间**必须**有一个空格

- 文件头可以注释。

- 类、类方法、类常量、类成员变量，函数，参数**必须**有文档类型的注释。

- 文档型注释遵从 [phpDocumentor][] 的规范。

- 类方法和函数的文档型注释分为 5 段，`摘要` `详情` `参数注释` `异常注释` `返回值注释`，每段之间**必须**有一个空行，`详情` 可以省略。

- PHP 内置类型的注释分为 `string` `int` `float` `bool` `resource` `null` `callable`，
根据 [phpDocumentor][] 规范，还支持 `mixed` `void` `object` `false` `true` `self` `$this`。

- 变量或参数类型如果是数组，**必须**写明成员类型，如 `int[]`，`User[]`，如果是混和类型则为 `mixed[]`。

- 建议设置 `todo` 提醒将来要完成的代码。

[phpDocumentor]: https://www.phpdoc.org/

范例：

注意空格，空行

```php
/**
 * 类说明
 */
class ClassName
{
    /**
     * 摘要......
     *
     * 详情............................
     * 详情............................
     *
     * @param User $user
     * @param int $argv1 变量说明
     * @param string $arg2
     * @param resource[] $arg3 optional
     *
     * @throws InvalidArgumentException 参数错误
     * @throws DBException 数据库操作失败
     *
     * @return false|array
     */
    public function fooBarBaz(User $user, $arg1, &$arg2, array $arg3 = [])
    {
        if (!is_numeric($arg1))
        {
            throw new InvalidArgumentException('arg1 invalid');
        }

        $user_id = $user->getId();
        $is_admin = $user->isAdmin(); // level 大于 3 的都是管理员

        if ($is_admin)
        {
            $user->foo($argv1);

            $list = $user->baz();

            // 只返回最新一天的记录
            $start_time = time() - 86400;
            $list = array_filter(
                function ($i) use ($start_time)
                {
                    return $i['update_time'] > $start_time;
                },
                $list
            );
            return $list;
        }
        else
        {
            // todo 如果不是管理员
            return false;
        }
    }
}
```


<a id="s10"></a>
## 10. 最佳实践 [^](#s0)

- **一定不能**使用执行命令的操作符，例如 `ls -ls`。

- 逻辑操作符**不该**使用 `and` `or` `xor`，**应该**使用 `&&` `||`。

- 局部变量**应该**定义在开始使用的地方。

- 函数和方法不要太长，**应该**把复杂的逻辑分解成独立函数或方法，即使函数或方法只有一个地方调用。

<a id="s11"></a>
## 11. 总结 [^](#s0)

以上规范难免有疏忽，本规范之后的修订将弥补不足之处。