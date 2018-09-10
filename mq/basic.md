## 使用场景
### 异步(结合注册发短信来谈,同步信息)
### 解耦(独立审批流)
### 削峰(订单生成,减少数据库压力)

## 常见mq
ActiveMQ，RabbitMQ，RocketMQ，Kafka,redis做消息队列
kafka比rabbitMQ性能更高，支持更高的吞吐量。  
rabbitMQ支持了AMQP协议，有确认机制，比kafka可用性更高。  
rabitMQ是推（push）的模型，kafka是拉（pull）的模型，rabbitMQ实时性更高；  
## rabbitmq
### AMQP协议
制定了broker一些组件规范,例如exchange,queue,bind,publisher,consumer
### 交换机模型
- direct
- fanout
- topic

### 消息的可靠投递
- consumer ack机制

- publisher ack机制

- 5个内部队列

- durable消息持久化
    - 持久化queue,exchange,message
    - 引入镜像队列

### 消息处理模式
- push
介绍：在消息累计不是很多的情况下可以快速的投递处理消息，实时性高，如果消费者的处理消息的能力很弱(一条消息需要很长的时间处理)，而消息中间件不断地向消费者Push消息，消费者的缓冲区可能会溢出。  
解决：设置一个limit值，当推送消息的数量到达了perfetch limit规定的数值时，消费者还没有向消息中间件返回ACK，消息中间件将不再继续向消费者推送消息。
- pull
介绍: 消息被处理依赖于消费者主动拉取，增加了消息被处理的延迟