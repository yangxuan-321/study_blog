package com.chief.mq;

import com.alibaba.rocketmq.client.consumer.DefaultMQPushConsumer;
import com.alibaba.rocketmq.client.consumer.listener.ConsumeConcurrentlyContext;
import com.alibaba.rocketmq.client.consumer.listener.ConsumeConcurrentlyStatus;
import com.alibaba.rocketmq.client.consumer.listener.MessageListenerConcurrently;
import com.alibaba.rocketmq.common.consumer.ConsumeFromWhere;
import com.alibaba.rocketmq.common.message.MessageExt;

import java.util.List;

/**
 * @author Kevin
 * @Title: Customer
 * @ProjectName java_study
 * @Description: TODO
 * @date 2019/1/31 16:45
 */
public class Customer {
    public static void main(String[] args) throws Exception {
        String consumerGroup = "ConsumerGroup";
        String nameServerAddr = "106.12.132.110:9876";
        //设置消费组 -- 消费组的概念就是
        //具有同样逻辑消费同样消息的consumer，可以归并为一个group。同一个group内的消费者，可以共同消费（CLUSTERING）对应topic的消息，达到分布式并行处理的功能。
        //消费同一类消息的多个 consumer 实例组成一个消费者组
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer(consumerGroup);
        //设置NameServer
        //NameServer: 提供轻量级的服务发现和路由
        //我们所有消息的生产和消费 都是先通过 nameserver 路由到 具体的broker 地址 进行消费的
        consumer.setNamesrvAddr(nameServerAddr);
        //这里设置的是一个consumer的消费策略
        //CONSUME_FROM_LAST_OFFSET 默认策略，从该队列最尾开始消费，即跳过历史消息
        //CONSUME_FROM_FIRST_OFFSET 从队列最开始开始消费，即历史消息（还储存在broker的）全部消费一遍
        //CONSUME_FROM_TIMESTAMP 从某个时间点开始消费，和setConsumeTimestamp()配合使用，默认是半个小时以前
        consumer.setConsumeFromWhere(ConsumeFromWhere.CONSUME_FROM_FIRST_OFFSET);
        //Topic 是一种消息的逻辑分类，比如说你有订单类的消息，也有库存类的消息，那么就需要进行分类，一个是订单 Topic 存放订单相关的消息，一个是库存 Topic 存储库存相关的消息。
        String topic = "topic_kevin";
        //设置consumer所订阅的Topic和Tag，*代表全部的Tag
        consumer.subscribe(topic, "*");

        //设置消费条数
        consumer.setConsumeMessageBatchMaxSize(100);

        //设置一个Listener，主要进行消息的逻辑处理
        consumer.registerMessageListener(new MessageListenerConcurrently() {
            public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs,
                                                            ConsumeConcurrentlyContext context) {

            System.out.println(Thread.currentThread().getName() + " Receive New Messages: " + msgs);
            System.out.println(msgs);
            //返回消费状态
            //CONSUME_SUCCESS 消费成功
            //RECONSUME_LATER 消费失败，需要稍后重新消费
            return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
            }
        });

        consumer.start();

        System.out.println("Consumer Started.");
    }
}
