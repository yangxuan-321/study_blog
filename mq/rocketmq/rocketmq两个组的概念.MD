https://m635674608.iteye.com/blog/2318002

ProducerGroup

    通常具有同样属性（处理的消息种类-topic、以及消息处理逻辑流程—分布式多个客户端）的一些producer可以归为同一个group。在事务消息机制中，如果某条发送某条消息的producer-A宕机，使得事务消息一直处于PREPARED状态并超时，则broker会回查 同一个group的其他producer，确认这条消息应该commit还是rollback。

ConsumerGroup

    具有同样逻辑消费同样消息的consumer，可以归并为一个group。同一个group内的消费者，可以共同消费（CLUSTERING）对应topic的消息，达到分布式并行处理的功能。