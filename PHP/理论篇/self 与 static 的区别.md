> PHP 官方文档：https://www.php.net/manual/zh/language.oop5.late-static-bindings.php



##### 官方解释：

使用 `self::` 或者 `__CLASS__` 对当前类的静态引用，取决于定义当前方法所在的类

`static::` 不再被解析为定义当前方法所在的类，而是在实际运行时计算的（`调用类`）。也可以称之为“静态绑定”，因为它可以用于（但不限于）静态方法的调用。

##### 示例如下：

```php
class Car
{
    public static function model()
    {
         self::getModel();
         // static::getModel(); 
    }

    protected static function getModel()
    {
        echo "I am a Car!";
    }

}

class Bmw extends Car
{
    protected static function getModel()
    {
        echo "I am a bmw!";
    }
}
```

使用 self:: 调用静态方法

```
Car::model();
// 结果：I am a Car!
Bmw::model();
// 结果：I am a Car!
```

调用 static:: 静态方法

```
Car::model();
// 结果：I am a Car!
Bmw::model();
// 结果：I am a bmw!
```

