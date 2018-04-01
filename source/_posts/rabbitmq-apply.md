---
title: Python:消息队列Rabbitmq基本使用
date: 2017-12-18 14:24:45
tags: rabbitmq
---


为什么用Rabbitmq instead of python queue ?

<!-- more -->

>是因为python queue 不能跨进程 


**队列的作用:**    
    1. 存储消息、数据
    2. 保证消息顺序 
    3. 保证数据的交付

```sh
# 斐波那契数列
1 1 2 3 5 8 13 ...
# 启动rabbitmq，并验证启动情况 
rabbitmq-server --detached &ps aux |grep rabbitmq
# 以服务的方式启动
service rabbitmq-server start
# 启用维护插件
rabbitmq-plugins enable rabbitmq_management 
# 重启
service rabbitmq-server restart

# erroe
pika.exceptions.ProbableAuthenticationError
# 解决方法
# 写入信息 并保存/关闭防火墙 'systemctl stop filewallf'
vim /etc/rabbitmq/rabbitmq.config
[{rabbit, [{loopback_users, []}]}].
# 查看当前队列
rabbitmqctl list_queues
```

## 基本使用实现
### 发送端

```python
import pika

# 创建连接
connection = pika.BlockingConnection(pika.ConnectionParameters('10.211.55.10'))
channel = connection.channel()

# 声明消息队列
channel.queue_declare(queue='hello')

# 发送消息到上面声明的hello队列，
# 其中exchange表示交换器，能精确指定消息应该发送到哪个队列，
# routing_key设置为队列的名称，
# body就是发送的内容，
channel.basic_publish(exchange='',routing_key='hello',body='Hello World!')

print('[x] Sent "Hello World!"')

# sh命令
# 用 rabbitmqctl list_queues 查看队列
'''
Listing queues
hello	1
'''
```

![](https://i.imgur.com/6vYEGPV.jpg)

### 接收端

```python
import pika

# 创建连接
connection = pika.BlockingConnection(pika.ConnectionParameters('10.211.55.10'))
channel = connection.channel()

# 声明消息队列
channel.queue_declare(queue='hello')

# 接收消息 回调函数
def callbcak(ch,method,properties,body):
    print("Received %r"%(body))

# 告诉rabbitmq使用callback来接收信息
channel.basic_consume(callbcak,queue='hello',no_ack=True)

#开始接收信息，并进入阻塞状态，队列里有信息才会调用callback进行处理。按ctrl+c退出。
channel.start_consuming()

# 终端会阻塞住
'''
Received b'Hello World!'
'''
```

## 工作队列
### 消息不丢失
#### 生产者
```python
for i in range(5):
    msg = ' '.join(sys.argv[1:])or 'Hello World! %s' % time.time()
    channel.basic_publish(exchange='',
                          routing_key='hello',
                          body=bytes(msg,encoding='utf8'),)
```
#### 消费者
- `no_ack=False` 消费者退出不消息不丢失

```python
# 修改回调函数
def callbcak(ch, method, properties, body):
    print("Received %r" % (body))
    time.sleep(5)
    print("[x] Done")
```

## 消息持久化
- 消息持久化存储,

>虽然消息反馈机制，但是如果rabbitmq自身挂掉的话，那么任务还是会丢失。所以需要将任务持久化存储起来。声明持久化存储：

```python
# 原队列
channel.queue_declare(queue='hello', durable=True)
```
但是这个程序会执行错误，因为hello这个队列已经存在，并且是非持久化的，rabbitmq不允许使用不同的参数来重新定义存在的队列。重新定义一个队列

```python
# 重新定义一个队列
channel.queue_declare(queue='task_queue', durable=True)
```
在发送任务的时候，用delivery_mode=2来标记任务为持久化存储：

```python
channel.basic_publish(exchange='',
       routing_key="task_queue",
       body=message,
       properties=pika.BasicProperties(
          delivery_mode = 2, # make message persistent
       ))
```

### 公平调度
`prefetch_count = 1`

虽然每个工作者是依次分配到任务，但是每个任务不一定一样。可能有的任务比较重，执行时间比较久；有的任务比较轻，执行时间比较短。如果能公平调度就最好了，使用basic_qos设置prefetch_count=1，使得rabbitmq不会在同一时间给工作者分配多个任务，即只有工作者完成任务之后，才会再次接收到任务。

```python
channel.basic_qos(prefetch_count=1)
```
- new_task.py完整代码

> 发送者/生产者

```python
import pika
import sys

connection = pika.BlockingConnection(pika.ConnectionParameters(
    host='10.211.55.10'))
channel = connection.channel()

channel.queue_declare(queue='task_queue', durable=True)

message = ' '.join(sys.argv[1:]) or "Hello World!"
channel.basic_publish(exchange='',
                      routing_key='task_queue',
                      body=message,
                      properties=pika.BasicProperties(
                          delivery_mode=2,  # make message persistent
                      ))
print(" [x] Sent %r" % (message,))
connection.close()
```

- worker.py完整代码

> 接受者/消费者

```python
import pika
import time

connection = pika.BlockingConnection(pika.ConnectionParameters(host='10.211.55.10'))
channel = connection.channel()

channel.queue_declare(queue='task_queue', durable=True)
print(' [*] Waiting for messages. To exit press CTRL+C')


def callback(ch, method, properties, body):
    print(" [x] Received %r" % (body,))
    time.sleep(6)
    print(" [x] Done",ch.basic_ack(delivery_tag=method.delivery_tag))


channel.basic_qos(prefetch_count=1)
channel.basic_consume(callback,
                      queue='task_queue')

channel.start_consuming()
```

## 广播
广播交换机的工作原理：消息发送端先将消息发送给交换机，交换机再将消息发送到绑定的消息队列，而后每个接收端都能从各自的消息队列里接收到信息。

![](https://i.imgur.com/mK7MEC5.jpg)

### 消费者/接收端receive.py代码分析

和最早的receive.py相比，主要是做了两个改动：

- 定义交换机
- 不使用hello队列了，随机生成一个临时队列，并绑定到交换机上


```python
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters(
    '10.211.55.10'))
channel = connection.channel()

# 定义交换机
channel.exchange_declare(exchange='messages', type='fanout')

# 随机生成队列，并绑定到交换机上
# 参数'exclusive=True'表示当接收端退出时，销毁临时产生的队列，这样就不会占用资源。
result = channel.queue_declare(exclusive=True)
queue_name = result.method.queue
channel.queue_bind(exchange='messages', queue=queue_name)


def callback(ch, method, properties, body):
    print(" [x] Received %r" % (body,))


channel.basic_consume(callback, queue=queue_name, no_ack=True)

print(' [*] Waiting for messages. To exit press CTRL+C')
channel.start_consuming()
```
执行`rabbitmqctl list_queues`

``` sh
task_queue	0
hello	5
# 定义了交换机
amq.gen-K0M17k_3LVYO0b7m0s-K1g	0
```

![](https://i.imgur.com/1EuVNfF.jpg)

### 生产者/发送端send.py代码分析
和最早的send.py相比，也只做了两个改动：

- 定义交换机
- 不是将消息发送到hello队列，而是发送到交换机

```python
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters(
    '10.211.55.10'))
channel = connection.channel()

# 定义交换机
# type='fanout' 表示广播的意思
channel.exchange_declare(exchange='messages', type='fanout')

# 将消息发送到交换机
# basic_publish方法的参数exchange被设定为相应交换机，
# 因为是要广播出去，发送到所有队列，所以routing_key就不需要设定了。
channel.basic_publish(exchange='messages', routing_key='', body='Hello World!')
print(" [x] Sent 'Hello World!'")
connection.close()
```

exchange如果为空，表示是使用匿名的交换机，在上面交换机信息的图片中可以看到有amq.*这样的交换机，就是系统默认的交换机了。routing_key在使用匿名交换机的时候才需要指定，表示发送到哪个队列的意思。第一篇的例子演示了这个功能。

打开另外一个终端，执行send.py，可以观察到receive.py接收到了消息。如果有多个终端执行receive.py，那么每个receive.py都会接收到消息。


## 组播/路由

### 生产者/send.py代码分析
和广播相比，改动点主要在两个方面：

- 设定交换机的类型（type）为direct。上一篇是设置为fanout，表示广播的意思，会将消息发送到所有接收端，这里设置为direct表示要根据设定的路由键来发送消息。
- 发送信息时设置发送的路由键。

```python
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters(
    '10.211.55.10'))
channel = connection.channel()

# 定义交换机，设置类型为direct
channel.exchange_declare(exchange='messages', type='direct')

# 定义三个路由键
routings = ['warning', 'error']

# 将消息依次发送到交换机，并设置路由键
for routing in routings:
    message = '%s message.' % routing
    channel.basic_publish(exchange='messages',
                          routing_key=routing,
                          body=message)
    print(message)

connection.close()
```

### 消费者/receive.py代码分析
和广播相比，改动点主要在三个方面：

- 设定交换机的类型（type）为direct。
- 增加命令行获取参数功能，参数即为路由键。
- 将队列绑定到交换机上时，设定路由键。


```python
import pika, sys

connection = pika.BlockingConnection(pika.ConnectionParameters(
    '10.211.55.10'))
channel = connection.channel()

# 定义交换机，设置类型为direct
channel.exchange_declare(exchange='messages', type='direct')

# 从命令行获取路由键参数，如果没有，则设置为info
routings = sys.argv[1:]
if not routings:
    routings = ['info']

# 生成临时队列，并绑定到交换机上，设置路由键
result = channel.queue_declare(exclusive=True)
queue_name = result.method.queue
for routing in routings:
    channel.queue_bind(exchange='messages',
                       queue=queue_name,
                       routing_key=routing)


def callback(ch, method, properties, body):
    print(" [x] Received %r" % (body,))


channel.basic_consume(callback, queue=queue_name, no_ack=True)

print(' [*] Waiting for messages. To exit press CTRL+C')
channel.start_consuming()
```

打开两个终端，一个运行代码python receive.py info warning，表示只接收info和warning的消息。另外一个终端运行send.py，可以观察到接收终端只接收到了info和warning的消息。如果打开多个终端运行receive.py，并传入不同的路由键参数，可以看到更明显的效果。

当接收端正在运行时，可以使用rabbitmqctl list_bindings来查看绑定情况。

## 按规则发送/正则
上面路由键/组播的功能，通过设置路由键，可以将消息发送到相应的队列，这里的路由键是要完全匹配，比如info消息的只能发到路由键为info的消息队列。

路由键模糊匹配，就是可以使用正则表达式，和常用的正则表示式不同，这里的话“#”表示所有、全部的意思；“*”只匹配到一个词。看完示例就能明白了。

### send.py代码分析
因为要进行路由键模糊匹配，所以交换机的类型要设置为topic，设置为topic，就可以使用#，*的匹配符号了。

```python
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters(
    '10.211.55.10'))
channel = connection.channel()

# 定义交换机，设置类型为topic
channel.exchange_declare(exchange='messages', type='topic')

# 定义路由键
# 四种类型的消息
routings = ['happy.work', 'happy.life', 'sad.work', 'sad.life']

# 将消息依次发送到交换机，并设定路由键
for routing in routings:
    message = '%s message.' % routing
    channel.basic_publish(exchange='messages',
                          routing_key=routing,
                          body=message)
    print(message)

connection.close()
```

### receive.py代码分析
类型要设定为topic就可以了。从命令行接收参数的功能稍微调整了一下，没有参数时报错退出。


```python
import pika, sys

connection = pika.BlockingConnection(pika.ConnectionParameters(
    '10.211.55.10'))
channel = connection.channel()

# 定义交换机，设置类型为topic
channel.exchange_declare(exchange='messages', type='topic')

# 从命令行获取路由参数，如果没有，则报错退出
routings = sys.argv[1:]
if not routings:
    # print(>> sys.stderr, "Usage: %s [routing_key]..." % (sys.argv[0],))
    print(sys.stderr, "Usage: %s [routing_key]..." % (sys.argv[0],))
    exit()

# 生成临时队列，并绑定到交换机上，设置路由键
result = channel.queue_declare(exclusive=True)
queue_name = result.method.queue
for routing in routings:
    channel.queue_bind(exchange='messages',
                       queue=queue_name,
                       routing_key=routing)


def callback(ch, method, properties, body):
    print(" [x] Received %r" % (body,))


channel.basic_consume(callback, queue=queue_name, no_ack=True)

print(' [*] Waiting for messages. To exit press CTRL+C')
channel.start_consuming()
```

### 实验运行
打开多个终端,分别传入不同的规则,观察结果
如:

```sh
python3 receive_topic.py "#"
python3 receive_topic.py "happy.*"
python3 receive_topic.py "*.work"
```

### 难点
1、发送信息时，如果不设置路由键，那么路由键设置为”*”的接收端是否能接收到消息？

>发送信息时，如果不设置路由键，默认是表示广播出去，理论上所有接收端都可以收到消息，但是笔者试了下，路由键设置为"*"的接收端收不到任何消息。

>只有发送消息时，设置路由键为一个词，路由键设置为"*"的接收端才能收到消息。在这里，每个词使用"."符号分开的。

2、发送消息时，如果路由键设置为”..”，那么路由键设置为”#.*”的接收端是否能接收到消息？如果发送消息时，路由键设置为一个词呢？
>两种情况，笔者都测试过了，可以的。

3、”a.*.#” 和”a.#”的区别
>- "a.#"只要字符串开头的一个词是a就可以了，比如a、a.haha、a.haha.haha。而这样的词是不行的，如abs、abc、abc.haha。

>- "a.*.#"必须要满足a.*的字符串才可以，比如a.、a.haha、a.haha.haha。而这样的词是不行的，如a。

## 远程结果返回RPC
Remote Producre Call
处理方法描述：
>发送端在发送信息前，产生一个接收消息的临时队列，该队列用来接收返回的结果。其实在这里接收端、发送端的概念已经比较模糊了，因为发送端也同样要接收消息，接收端同样也要发送消息，所以这里笔者使用另外的示例来演示这一过程。

### compute.py代码分析

```python
import pika

# 连接rabbitmq服务器
connection = pika.BlockingConnection(pika.ConnectionParameters(
    host='10.211.55.10'))
channel = connection.channel()

# 定义队列
channel.queue_declare(queue='compute_queue')
print(' [*] Waiting for n')


# 将n值加1
def increase(n):
    return n + 1


# 定义接收到消息的处理方法
def request(ch, method, properties, body):
    print(" [.] increase(%s)" % (body,))

    response = increase(int(body))

    # 将计算结果发送回控制中心
    ch.basic_publish(exchange='',
                     routing_key=properties.reply_to,
                     body=str(response))
    ch.basic_ack(delivery_tag=method.delivery_tag)


channel.basic_qos(prefetch_count=1)
channel.basic_consume(request, queue='compute_queue')

channel.start_consuming()
```

### center.py代码分析

```python
import pika


class Center(object):
    def __init__(self):
        self.connection = pika.BlockingConnection(pika.ConnectionParameters(
            host='10.211.55.10'))

        self.channel = self.connection.channel()

        # 定义接收返回消息的队列
        result = self.channel.queue_declare(exclusive=True)
        self.callback_queue = result.method.queue

        self.channel.basic_consume(self.on_response,
                                   no_ack=True,
                                   queue=self.callback_queue)

    # 定义接收到返回消息的处理方法
    def on_response(self, ch, method, props, body):
        self.response = body

    def request(self, n):
        self.response = None
        # 发送计算请求，并声明返回队列
        self.channel.basic_publish(exchange='',
                                   routing_key='compute_queue',
                                   properties=pika.BasicProperties(
                                       reply_to=self.callback_queue,
                                   ),
                                   body=str(n))
        # 接收返回的数据
        while self.response is None:
            self.connection.process_data_events()
        return int(self.response)


center = Center()

print(" [x] Requesting increase(30)")
response = center.request(30)
print(" [.] Got %r" % (response,))
```
上面代码定义了接收返回数据的队列和处理方法，并且在发送请求的时候将该队列赋值给`reply_to`，在计算节点代码中就是通过这个参数来获取返回队列的。


## 相互关联编号correlation id
`correlation id`运行原理：
>控制中心发送计算请求时设置correlation id，而后计算节点将计算结果，连同接收到的correlation id一起返回，这样控制中心就能通过correlation id来标识请求。其实correlation id也可以理解为请求的唯一标识码。

示例内容：
>控制中心开启多个线程，每个线程都发起一次计算请求，通过correlation id，每个线程都能准确收到相应的计算结果。

### compute.py代码分析
和上面相比，只需修改一个地方：
>将计算结果发送回控制中心时，增加参数correlation_id的设定，该参数的值其实是从控制中心发送过来的，这里只是再次发送回去。代码如下：

```python
import pika

# 连接rabbitmq服务器
connection = pika.BlockingConnection(pika.ConnectionParameters(
    host='10.211.55.10'))
channel = connection.channel()

# 定义队列
channel.queue_declare(queue='compute_queue')
print(' [*] Waiting for n')


# 将n值加1
def increase(n):
    return n + 1


# 定义接收到消息的处理方法
def request(ch, method, props, body):
    print(" [.] increase(%s)" % (body,))

    response = increase(int(body))

    # 将计算结果发送回控制中心，增加correlation_id的设定
    ch.basic_publish(exchange='',
                     routing_key=props.reply_to,
                     properties=pika.BasicProperties(correlation_id= \
                                                         props.correlation_id),
                     body=str(response))
    ch.basic_ack(delivery_tag=method.delivery_tag)


channel.basic_qos(prefetch_count=1)
channel.basic_consume(request, queue='compute_queue')

channel.start_consuming()
```

### center.py代码分析
控制中心代码稍微复杂些，其中比较关键的有三个地方：

- 使用python的uuid来产生唯一的correlation_id。
- 发送计算请求时，设定参数correlation_id。
- 定义一个字典来保存返回的数据，并且键值为相应线程产生的correlation_id。

```python
import pika, threading, uuid


# 自定义线程类，继承threading.Thread
class MyThread(threading.Thread):
    def __init__(self, func, num):
        super(MyThread, self).__init__()
        self.func = func
        self.num = num

    def run(self):
        print(" [x] Requesting increase(%d)" % self.num)
        response = self.func(self.num)
        print(" [.] increase(%d)=%d" % (self.num, response))


# 控制中心类
class Center(object):
    def __init__(self):
        self.connection = pika.BlockingConnection(pika.ConnectionParameters(
            host='10.211.55.10'))

        self.channel = self.connection.channel()

        # 定义接收返回消息的队列
        result = self.channel.queue_declare(exclusive=True)
        self.callback_queue = result.method.queue

        self.channel.basic_consume(self.on_response,
                                   no_ack=True,
                                   queue=self.callback_queue)

        # 返回的结果都会存储在该字典里
        self.response = {}

    # 定义接收到返回消息的处理方法
    def on_response(self, ch, method, props, body):
        self.response[props.correlation_id] = body

    def request(self, n):
        corr_id = str(uuid.uuid4())     #  产生 id
        self.response[corr_id] = None

        # 发送计算请求，并设定返回队列和correlation_id
        self.channel.basic_publish(exchange='',
                                   routing_key='compute_queue',
                                   properties=pika.BasicProperties(
                                       reply_to=self.callback_queue,
                                       correlation_id=corr_id,
                                   ),
                                   body=str(n))
        # 接收返回的数据
        while self.response[corr_id] is None:
            self.connection.process_data_events()
        return int(self.response[corr_id])


center = Center()
# 发起5次计算请求
nums = [10, 20, 30, 40, 50]
threads = []
for num in nums:
    threads.append(MyThread(center.request, num))
for thread in threads:
    thread.start()
for thread in threads:
    thread.join()
```


## 参考资料
<http://blog.csdn.net/chenjiebin/article/details/8253433>

