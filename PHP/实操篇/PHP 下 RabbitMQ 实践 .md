> 原文链接：https://www.helloweba.net/php/625.html



消息队列（Message Queue）是一种应用间的通信方式，消息发送后可以立即返回，由消息系统来确保消息的可靠传递。消息发布者只管把消息发布到 MQ 中而不用管谁来取，消息使用者只管从 MQ 中取消息而不管是谁发布的。这样发布者和使用者都不用知道对方的存在。

简单的消息队列，我们完全可以使用 Redis 实现，而相对复杂的需求，比如消息确认、消息持久化、高可用等需要用 RabbitMQ 这样的大器来做比较合适。

我们结合实例给大家讲解使用 PHP 处理 RabbitMQ 消息队列的应用。

#### **安装 php-amqplib**

php-amqplib 是一个纯 PHP 库，使用它，基于 PHP 的脚本客户端就可以轻松的连接和操作 RabbitMQ。我们使用`composer`来安装。

```
composer require php-amqplib/php-amqplib
```

#### **示例说明**

生产者（Producer）和消费者（Consumer）是消息队列的基本概念，生产者是指生产消息的一方，也是消息发送方，消费者就是消费消息的一方，也是消息接收方，队列就是存储消息的一个缓存区。

本实例将由生产者发送很多消息给消息队列，由多个消费者来消费队列中的消息。我们可以想象这样的场景：皮鞋生产打包打包车间，不断有成品鞋进入传送带（消息队列）等待操作工人（消费者）将皮鞋打包。

因为等待打包的鞋子特别多，我们需要安排多个打包工人在传送带两边，及时从传送带取出成品鞋，然后装箱打包。我们要求是要确保工人最后打包好的皮鞋数量一双不少，不能因为打包工人操作慢或者个人原因暂时离开生产线，导致最终打包数不一致。

![](https://mmbiz.qpic.cn/mmbiz_jpg/QibLP1rpwH8v3ibgR8cMws9scYju2kZ5m3upBwt8hzjRN0EX8dlvQlNN2YRco8rU9ibUzOaPXZ7LyondVJEJOq78Q/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

#### **消息发送**

生产者将消息发送给队列，至于谁来消费（处理）这些消息，生产者不管。

消息队列（MQ），用来保存消息直到发送给消费者。它是消息的容器，也是消息的终点。一个消息可投入一个或多个队列。消息一直在队列里面，等待消费者连接到这个队列将其取走。

消息到达队列中后，如果没有一个消费者来处理消息的话，我们希望队列中的消息不要丢弃，也就是消息持久化。在生产者和消费者中都要将`queue_declare`第 3 个参数设置为 true，表示让消息队列持久化。

```
$channel->queue_declare($queue, false, true, false, false); 

```

此外，我们可以确保即使 RabbitMQ 重启了，消息队列不会丢失，在生产者端设置：`'delivery_mode' => AMQPMessage::DELIVERY_MODE_PERSISTENT`。

现在我们建立生产者文件 sender.php，我们假设服务端已经安装好 RabbitMQ，并且开放好对应端口。

```php
<?php
/**
 * @Author: Helloweba
 * @sender.php
 * @消息生产者-分发任务
 */

require_once __DIR__ . '/vendor/autoload.php';
use PhpAmqpLib\Connection\AMQPStreamConnection;
use PhpAmqpLib\Message\AMQPMessage;

$queue = 'worker';

//$connection = new AMQPStreamConnection('localhost', 5672, 'guest', 'guest');
$connection = new AMQPStreamConnection(
    '192.168.0.100', 
    56720, 
    'helloweba',  //user
    'helloweba',  //password
    'test'  //vhost
);
$channel = $connection->channel();

$channel->queue_declare($queue, false, true, false, false); //第3个参数设置为true，表示让消息队列持久化

for ($i = 0; $i < 100; $i++) { 
    $arr = [
        'id' => 'message_' . $i,
        'order_id' => str_replace('.', '' , microtime(true)) . mt_rand(10, 99) . $i,
        'content' => 'helloweba-' . time()
    ];
    $data = json_encode($arr);
    $msg = new AMQPMessage($data, ['delivery_mode' => AMQPMessage::DELIVERY_MODE_PERSISTENT]); ////设置rabbitmq重启后也不会丢失队列，或者设置为'delivery_mode' => 2
    $channel->basic_publish($msg, '', $queue);

    echo 'Send message: ' . $data . PHP_EOL;
}

$channel->close();
$connection->close();


```

上述代码中，我们模拟了生产者向队列中发送了 100 条订单消息。

#### **消息接收**

消费者是指完成消息的接收和处理的客户端程序，消费者就如同生产线上的操作工人，他们按照操作规程从传送带上取出产品后有序的完成后续工作任务。

实际项目中，如果消费者处理消息能力不够时，就要开启多个消费者来消费队列中的消息。默认情况下，RabbitMQ 将会把队列中的消息平均分配给每个消费者。如果消费者要对分配到的消息任务处理时间很长（耗时任务），那么处理消息任务的时候就有可能会遇到意外。

比如某个消费者断电了，或者出故障了，那它正在处理的消息会怎么办？这里就是 RabbitMQ 的消息确认机制，为了保证数据不丢失，RabbitMQ 会将未处理完的消息分配给下一个消费者处理。

此外 RabbitMQ 还可以设置公平分配消息任务，不会给某个消费者同时分配多个消息处理任务，因为消费者无法同时处理多个消息任务。

换句话说，RabbitMQ 在处理和确认消息之前，不会向消费者发送新的消息，而是将消息分发给下一个不忙的消费者。

```
$channel->basic_qos(null, 1, null); //处理和确认完消息后再消费新的消息

```

我们现在建立消费者文件 receiver.php，代码如下：

```
<?php
/**
 * @Author: Helloweba
 * @receiver.php
 * @消息消费者-接收端
 */

require_once __DIR__ . '/vendor/autoload.php';
use PhpAmqpLib\Connection\AMQPStreamConnection;

$queue = 'worker';

//$connection = new AMQPStreamConnection('localhost', 5672, 'guest', 'guest');
$connection = new AMQPStreamConnection('192.168.0.100', 56720, 'helloweba', 'helloweba', 'test');
$channel = $connection->channel();

$channel->queue_declare($queue, false, true, false, false);

echo ' [*] Waiting for messages. To exit press CTRL+C' . PHP_EOL;

$callback = function($msg){
    echo " Received message：", $msg->body, PHP_EOL;
    sleep(1);  //模拟耗时执行
    $msg->delivery_info['channel']->basic_ack($msg->delivery_info['delivery_tag']);
};

$channel->basic_qos(null, 1, null); //处理和确认完消息后再消费新的消息
$channel->basic_consume($queue, '', false, false, false, false, $callback); //第4个参数值为false表示启用消息确认

while(count($channel->callbacks)) {
    $channel->wait();
}

$channel->close();
$connection->close();


```

#### **模拟测试**

现在我们运行多个消费者终端，可以打开多个 ssh 客户端，client1 和 client2 运行：

```
php receive.php

```

然后再开个终端，运行生产者：

```
php sender.php

```

由于消费者是阻塞运行的，他们会一直等待队列中的消息，当有消息就会去取出来处理。我们可以模拟将其中某个客户端中断，即断开某个消费者。

然后再看消息是不是被其他消费者接收处理了。同样我们可以模拟将客户端全部重启，看看队列中的消息是否没有丢失。  

当 client1 中断连接 RabbitMQ 后，再次运行连接 RabbitMQ，在 client2 中看到的消息处理情况，注意看图中的消息 id。



client1:

![](https://mmbiz.qpic.cn/mmbiz_jpg/QibLP1rpwH8v3ibgR8cMws9scYju2kZ5m3geMlYSHCDmnomYbCNYrY9ic32MVElBcW4NR0U4U6aSLtClbSxYAq2VQ/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

client2:

![](https://mmbiz.qpic.cn/mmbiz_jpg/QibLP1rpwH8v3ibgR8cMws9scYju2kZ5m3xCk1wibgOia1iauxgibxibYabaN45IXibsd7ALwmVxNt2wGSib8faKrysgfuw/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)



  


