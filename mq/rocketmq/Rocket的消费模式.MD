**细谈RocketMQ的消费模式**

    首先明确一点，RocketMQ 是基于发布订阅模型的消息中间件。所谓的发布订阅就是说，consumer 订阅了 broker 上的某个 topic，当 producer 发布消息到 broker 上的该 topic 时，consumer 就能收到该条消息。
    
    
    1.  集群消费    (有多个消费者实例, 但是一条消息只会被一个消费者实例 消费)
    2.  广播消费    (有多个消费者实例, 但是一条消息会被所有的消费者实例 消费一次)
    
    
    Rocketmq消费分为push和pull两种方式，
        push为被动消费类型
        pull为主动消费类型
        
        push方式最终还是会从broker中pull消息。不同于pull的是，push首先要注册消费监听器，当监听器处触发后才开始消费消息，所以被称为“被动”消费。
        
        
        
        https://www.cnblogs.com/chenjunjie12321/p/7922362.html