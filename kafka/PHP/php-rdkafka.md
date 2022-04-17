#### 安装

##### PHP-5.3.3

php-rdkafka `<=3.0.5`

librdkafka `>=0.11.x`

##### PHP-7.x

使用最新版本即可

#### 配置

参考 https://github.com/edenhill/librdkafka/blob/master/CONFIGURATION.md

分为 global 和 topic 配置

#### producer-发消息

##### 消息分区

https://www.jianshu.com/p/91277c38fda6

##### 几个核心方法

poll

flush

getOutQLen

purge

#### consumer-消费消息

##### High level  VS Low level

`High level`

高级模式，必须指定 group id，自动加入消费者组，自动保存和提交 offset。不支持批量消费。

 `Low level`

低级模式，不需要指定 group id，手动指定消费分区和消费起始点。支持批量消费。

