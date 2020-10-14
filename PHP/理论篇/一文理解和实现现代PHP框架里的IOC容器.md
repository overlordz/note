> 原文地址 [mp.weixin.qq.com](https://mp.weixin.qq.com/s?__biz=Mzg5MzEyNDQ1Mw==&mid=2247483736&idx=1&sn=0097da2b460a9f385acd58e387c46876&chksm=c032eb52f74562442bae9a5f667b51cd6adcd68c5ca149d9353ba30837a509aa8271772a3c54&mpshare=1&scene=23&srcid=&sharer_sharetime=1589505992716&sharer_shareid=4d83e3eef79e57c6fb91f3d92d4dd880#rd)

### **容器是什么？**

相信很多人听说过依赖注入，依赖注入实现的基础条件离不开容器，容器就是用来管理类依赖和注入的，负责服务的管理和解耦组件，最简单的理解我们可以把容器理解成一个超级大、专门存对象的数组。

![](https://mmbiz.qpic.cn/mmbiz_jpg/r2pUYRoxdhHh0779NIGqpgTERo8NoB1Mg85iaIHCLTs6wg8Ge2Oe0gtuCyrpia3ib93XgU9fHja8O8BWENK6I53cg/640?wx_fmt=jpeg)

   图 1  

如图所示调用者通过容器的标示获取到对象实例，图里可以看出来，可以通过 `::class` 的方式来获取也可以直接通过对象标示获取实例对象。

**IOC 是****什么？**  

大家可能都听说过 IOC 容器，IOC 的全称是：(Inversion Of Control，反转控制)。

我们来理解一下什么是反转控制，在我们传统编码中我们在类与类之间的依赖通常是我们通过编码的方式 new 出来对象再传递的，而使用控制反转我们可以把对象的控制权交给容器或者框架去实现。目的是为了让我们不需要硬编码去创建对象，看图 1 可以知道，容器里面存放着很多对象，当我们要使用的时候可以直接去用。而容器里面的对象不需要我们在代码中编码创建。在需要某个类对象的时候会去容器里面获取对象，如果对象不存在则会自动创建。这就是省略了我们在代码里面去创建对象的过程，由容器去帮我们实现这个创建的过程，这就叫反转控制。一句话总结 IOC：把创建对象的控制权转移给容器实现类的实例化。

例如：没有使用 IOC 的情况下，我们想要创建类

```
<?php
class Sunny{
}
$sunny = new Sunny();
```

我们需要手动去 new 一个类，这种情况就是硬编码在代码里面去实现的。

而使用 IOC 容器的代码则可以这样写。  

```
<?php
class Sunny{
}
$sunny = Container::getBean(Sunny::class);
```

在容器的内部去帮我们实现这个类，有同学看到这里可能会有疑问，我使用 new Sunny 不是代码写得更短更简单吗？我们看完依赖注入再看一个例子。

**依赖注入**

现在知道了 IOC 是什么，那么一个新的问题出来了，我们在创建类的时候有些类的构造方法会需要我们传递参数怎么办？通过 IOC 的学习我们知道了 IOC 容器会帮我们解决这个对象实例创建的问题，那么在容器里面创建对象的时候发现类有其他依赖则会进行依赖查找，容器寻找需要对象的过程，称为 DL(Dependency Lookup, 依赖查找)。而把需要的依赖注入到代码片段中这个称为 DI(Dependency Injection, 依赖注入)。

例如 IOC 里面说到的 new Sunny 这个例子。如果在类与类之间有多重依赖。

```
<?php
class Computer{
    public function run(){
        echo "编程中....\n";
    }
}
class Sunny{
    private $computer;
    public function __construct(Computer $computer){
        $this->computer = $computer;
    }
    public function program(){
        $this->computer->run();
    }
}
$sunny = new Sunny(new Computer());
$sunny->program();
```

这里可以看到 Sunny 这个类想要编程依赖类 Computer 这个类，而如果使用 IOC 容器实现依赖注入的话，代码就简单了。  

```
<?php
class Computer{
    public function run(){
        echo "编程中....\n";
    }
}
class Sunny{
    private $computer;
    public function __construct(Computer $computer){
        $this->computer = $computer;
    }
    public function program(){
        $this->computer->run();
    }
}
$sunny = Container::getBean(Sunny::class);
$sunny->program();
```

### 一句话总结：解决创建类实例当中对其他类的依赖，动态的向某个对象提供它所需要的其他对象。

### **依赖倒置**

依赖倒置解决的问题是松耦各个模块之间的重度依赖，上层模块不应该依赖底层模块，它们都应该依赖于抽象。通常简单的理解依赖倒置就是面向接口或者面向抽象来进行编程。我们通过下面的例子来看看面向接口编程。

```
class Cache{
    public function set($key,$value){
        $redis = new CFile();
        $redis->set($key,$value);
    }
}
class CFile{
    public function set($key,$value){
        echo "file:{$key}->{$value}\n";
    }
}
$cache = new Cache();
$cache->set("name","sunny");
```

上面的这段代码看似没有什么大问题，但是如果有一天把文件缓存改成 Redis 缓存呢？

```
class Cache{
    public function set($key,$value){
        $redis = new CRedis();
        $redis->set($key,$value);
    }
}
class CRedis{
    public function set($key,$value){
        echo "redis:{$key}->{$value}\n";
    }
}
$cache = new Cache();
$cache->set("name","sunny");
```

通过这段代码可以看出来当一个缓存使用的驱动改变了的时候，Cache 的代码也必须作出相应的改变，因为代码写死在调用者身上了，耦合度变得高了。再对代码进行改造一样，让程序员面向 interface 编程，让代码变得更通用，更规范。

```
interface ICache{
    public function set($key,$value);
}
class CRedis implements ICache {
    public function set($key,$value)
	{
        echo "redis:{$key}->{$value}\n";
    }
}
class CFile implements ICache{
    public function set($key,$value)
	{
        echo "file:{$key}->{$value}\n";
    }
}
class Cache{
    private $drive;
    public function __construct(ICache $drive)
	{
        $this->drive = $drive;
    }
    public function set($key,$value){
        $this->drive->set($key,$value);
    }
}
$cache = new Cache(new CFile());
$cache->set("name","sunny");
```

很多人看到这段代码的时候想着，那我在构造方法直接把要的对象传进去不就好了吗？为什么还要定义一个 interface 呢？其实定义 interface 是为了规范代码，不管你使用哪个驱动，只要实现了我这个 interface 的都可以用，没有 interface 开发者在开发驱动的时候就会不知道这个驱动里面该有什么方法。当我们使用 interface 之后大家只要面向接口编程，Cache 完全不管类是怎么实现的，Cache 只是根据 interface 的方法进行操作。

一句话总结：依赖倒置实现松耦合

**实战：根据容器原理实现容器**
=================

```
<?php
class Container
{
    // 当前容器对象
    private static $instance;
    // 存放在容器里面到实例
    protected $instances = [];
    private function __construct()
	{
    }
    public static function getInstance()
	{
        if (!self::$instance) {
            self::$instance = new static();
        }
        return self::$instance;
    }
    /**
     * 获取对象实例
     * @param $key
     * @return mixed
     */
    public function get($key)
	{
        if (isset($this->instances[$key])) {
            return $this->instances[$key];
        }
    }
    /**
     * 绑定对象、闭包、类到容器
     * @param $key
     * @param null $concrete
     * @return Container
     */
    public function bind($key, $concrete = null)
	{
        if ($concrete instanceof Closure) {
            $this->instances[$key] = $concrete;
        } elseif (is_object($concrete)) {
            $this->instances[$key] = $concrete;
        }
        return $this;
    }
}
class Sunny
{
    public function getName()
	{
        echo time() . "\n";
    }
}
$app = Container::getInstance();
$sunny = $app->bind(Sunny::class,new Sunny());
$sunny = $app->get(Sunny::class);
$sunny->getName();
```

**实战：实现依赖注入**
=============

### Container.php

```
<?php
class Container
{
    // 当前容器对象
    private static $instance;
    // 存放在容器里面到实例
    protected $instances = [];
    private function __construct()
	{
    }
    public static function getInstance()
	{
        if (!self::$instance) {
            self::$instance = new static();
        }
        return self::$instance;
    }
    /**
     * 获取对象实例
     * @param $key
     * @return mixed
     * @throws ReflectionException
     */
    public function get($key)
	{
        if (isset($this->instances[$key])) {
            return $this->instances[$key];
        }
        return $this->make($key);
    }
    /**
     * 绑定对象、闭包、类到容器
     * @param $key
     * @param null $concrete
     * @return Container
     * @throws ReflectionException
     */
    public function bind($key, $concrete = null)
	{
        if ($concrete instanceof Closure) {
            $this->instances[$key] = $concrete;
        } elseif (is_object($concrete)) {
            $this->instances[$key] = $concrete;
        } else {
            $this->make($key, $concrete);
        }
        return $this;
    }
    /**
     * 创建类绑定到类实例
     * @param $abstract
     * @param null $atgs
     * @return mixed
     * @throws ReflectionException
     */
    public function make($abstract, $atgs = null)
	{
        if (isset($this->instances[$abstract])) {
            return $this->instances[$abstract];
        }
        $object = $this->invokeClass($abstract);
        $this->instances[$abstract] = $object;
        return $object;
    }
    /**
     * 反射解析类
     * @param $abstract
     * @return object
     * @throws ReflectionException
     */
    public function invokeClass($abstract)
	{
        $reflectionClass = new \ReflectionClass($abstract);
        // 获取构造方法
        $construct = $reflectionClass->getConstructor();
        // 获取参数得到实例
        $params = $construct ? $this->parserParams($construct) : [];
        $object = $reflectionClass->newInstanceArgs($params);
        return $object;
    }
    /**
     * 解析构造方法参数
     * @param $reflect
     * @return array
     * @throws ReflectionException
     */
    public function parserParams(ReflectionMethod $reflect)
	{
        $args = [];
        $params = $reflect->getParameters();
        if (!$params) {
            return $args;
        }
        if (count($params) > 0) {
            foreach ($params as $param) {
                $class = $param->getClass();
                if ($class) {
                    $args[] = $this->make($class->getName());
                    continue;
                }
                // 获取变量的名称
                $name = $param->getName();
                // 默认值
                $def = null;
                // 如果有默认值，从默认值获取类型
                if ($param->isOptional()) {
                    $def = $param->getDefaultValue();
                }
                $args[] = $_REQUEST[$name] ?? $def;
            }
        }
        return $args;
    }
}
```

### Test.php

```
<?php
class Test
{
    public $name;
    private $test1;
    public function __construct(Test1 $test1)
	{
        $this->test1 = $test1;
        $this->name = $this->test1->getName();
    }
}
```

### Test1.php

```
<?php
class Test1
{
    public function getName(){
        return "test1返回的名字";
    }
}
```

### Sunny.php

```
<?php
require_once "./Container.php";
require_once "./Test.php";
require_once "./Test1.php";
class Sunny
{
    private $test;
    public function __construct(Test $test)
	{
        $this->test = $test;
    }
    public function getName()
	{
        echo "获取test里面的name：{$this->test->name}\n";
    }
}
$app = Container::getInstance();
$sunny = $app->get(Sunny::class);
$sunny->getName();
```
