**RocketMQ的相关概念介绍及集群搭建**

    1.RocketMQ简介
        阿里出品
    
    2.选择的理由
        2.1 强调集群无单点，可扩展，任意一点高可用，水平可扩展
            方便集群配置，而且容易扩展（横向和纵向），通过slave的方式每一点都可以实现高可用
        2.2 支持上万个队列，顺序消息
            顺序消费是实现在同一队列的，如果高并发的情况就需要队列的支持，rocketmq可以满足上万个队列同时存在
        2.3 任性定制你的消息过滤
            rocketmq提供了两种类型的消息过滤，也可以说三种可以通过topic进行消息过滤、可以通过tag进行消息过滤、还可以通过filter的方式任意定制过滤
        2.4 消息的可靠性（无Buffer，持久化，容错，回溯消费）
            消息无buffer就不用担心buffer回满的情况，rocketmq的所有消息都是持久化的，生产者本身可以进行错误重试，发布者也会按照时间阶梯的方式进行消息重发，消息回溯说的是可以按照指定的时间进行消息的重新消费，既可以向前也可以向后（前提条件是要注意消息的擦除时间）
        2.5 海量消息堆积能力，消息堆积后，写入低延迟
            针对于provider需要配合部署方式，对于consumer，如果是集群方式一旦master返现消息堆积会向consumer下发一个重定向指令，此时consumer就可以从slave进行数据消费了
        2.6 分布式事务
            我个人感觉rocketmq对这一块说的不是很清晰，而且官方也说现在这块存在缺陷（会令系统pagecache过多），所以线上建议还是少用为好
        2.7 消息失败重试机制
            针对provider的重试，当消息发送到选定的broker时如果出现失败会自动选择其他的broker进行重发，默认重试三次，当然重试次数要在消息发送的超时时间范围内。
            针对consumer的重试，如果消息因为各种原因没有消费成功，会自动加入到重试队列，一般情况如果是因为网络等问题连续重试也是照样失败，所以rocketmq也是采用阶梯重试的方式。
        2.8 定时消费
            除了上面的配置，在发送消息是也可以针对message设置setDelayTimeLevel
        2.9 活跃的开源社区
            现在rocketmq成为了apache的一款开源产品，活跃度也是不容怀疑的
        2.10 成熟度（经过双十一考验）
            针对本身的成熟度，我们看看这么多年的双十一就可想而知了
        
    3.相关概念
        3.1 NameServer
            可以理解成类似zk的注册中心
         
        3.2 Push Consumer
            Consumer 的一种，应用通常通过 Consumer 对象注册一个 Listener 接口，一旦收到消息，Consumer 对象立刻回调 Listener 接口方法，类似于activemq的方式
            
        3.3 Pull Consume
            Consumer 的一种，应用通常主动调用 Consumer 的拉消息方法从 Broker 拉消息，主动权由应用控制
            
        3.4 Producer Group
            一类producer的集合名称，这类producer通常发送一类消息，且发送逻辑一致
            
        3.5 Consumer Group
            同上，consumer的集合名称
            
        3.6 Broker
            消息中转的角色，负责存储消息（实际的存储是调用的store组件完成的），转发消息，一般也称之为server，同jms中的provider
            
        3.7 Message Filter
            可以实现高级的自定义的消息过滤
            
        3.8 Master/Slave
            集群的主从关系，broker的name相同，brokerid=0的为主master，大于0的为从slave，可以一主多从，但一从只能有一主