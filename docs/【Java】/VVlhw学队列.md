# VVlhw学队列

前提：

复习Condition、wait、notifyAll

各种方法分类异常、特殊值、阻塞

阻塞队列、消息队列的区别，既然阻塞队列例子是生产者消费者，那消息队列好像也是



消息队列

消息队列中间件是分布式系统中重要的组件，主要解决应用耦合，异步消息，流量削锋等问题

实现高性能，高可用，可伸缩和最终一致性架构

使用较多的消息队列有ActiveMQ，RabbitMQ，ZeroMQ，Kafka，MetaMQ，RocketMQ

MQ的用处：

- 异步处理
- 应用解耦
- 流量削锋
- 消息通讯

待整理好文：https://www.cnblogs.com/pony1223/p/9521613.html



> MQ关键：如何实现即时消费，如何实现ACK机制

简单概念和ACK机制：RabbitMQ有，RocketMQ没有？ https://www.jianshu.com/p/6da2fd387efe

**ACK——消息确认机制**：

各种消息队列的区别：ActiveMQ用的不多，RabbitMQ（erlang语言劝退），常用RocketMQ，大数据Kafka https://blog.csdn.net/wangbo0130/article/details/91865438





阻塞队列

之前线程的wait和notify我们程序员需要自己控制，但有了这个阻塞队列之后我们程序员就不用担心了，阻塞队列会自动管理

阻塞队列应用最广泛的是生产者和消费者模式

待看：https://www.jianshu.com/p/32665a52eba1 、 https://baijiahao.baidu.com/s?id=1659873724613844132&wfr=spider&for=pc