package com.chief.mq;

import com.alibaba.rocketmq.client.producer.DefaultMQProducer;
import com.alibaba.rocketmq.client.producer.SendResult;
import com.alibaba.rocketmq.common.message.Message;

/**
 * @author Kevin
 * @Title: Producer
 * @ProjectName java_study
 * @Description: TODO
 * @date 2019/1/30 19:26
 */
public class Producer {
    public static void main(String[] args) throws Exception {
        DefaultMQProducer producer = new DefaultMQProducer("ProducerGroup");
        //指定mqserver的ip:port
        producer.setNamesrvAddr("106.12.132.110:9876");
        producer.start();

        String topic = "topic_kevin";
        String tag = "tag_kevin";

        //发送100条消息
        for (int i = 0; i < 5; i++){
            try {
                String content = "我爱北京天安门,天安门上太阳升。" + i;
                Message msg = new Message(topic, tag, content.getBytes("UTF-8"));
                //发送消息
                SendResult send = producer.send(msg);
                System.out.println(send);
            }catch (Exception e){
                e.printStackTrace();
            }
        }
    }
}
