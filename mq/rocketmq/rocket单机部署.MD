**rocket单机部署**
    
    
    //第一步就不要做了 第二步骤和第三步骤的时候 发现没有镜像会自动去 拉取
    # 1. 拉去rocketmq的镜像
        # docker pull foxiswho/rocketmq
    docker --link 的用法
        https://www.jianshu.com/p/21d66ca6115e
    docker -p 1234:1234 -p 2345:2345
        启用多个端口对外提供服务
    docker exec -it c7188be9bd7b /bin/bash
        进入某个已经运行的docker容器的bash界面
    
    2. 启动server 容器
        docker run -d -p 9876:9876 --name rmqserver  foxiswho/rocketmq:server
        
    3. 启动broker 容器 (映射多个端口 10911和10909)
        docker run -d -p 10911:10911 -p 10909:10909 --name rmqbroker --link rmqserver:namesrv -e "NAMESRV_ADDR=namesrv:9876" -e "JAVA_OPTS=-Duser.home=/opt"  -e "JAVA_OPT_EXT=-server -Xms128m -Xmx128m -Xmn128m" foxiswho/rocketmq:broker
        
    4. broker 配置文件位置 (容器内) 可以不用做
        /etc/rocketmq/broker.conf
        具体步骤：
            4.1 docker exec -it broker容器的id /bin/bash
            4.2 vi /etc/rocketmq/broker.conf
            
    5. 启动rocketmq console 容器
        docker run --name rmqconsole -p 8180:8080 --link rmqserver:namesrv -e "JAVA_OPTS=-Drocketmq.namesrv.addr=namesrv:9876 -Dcom.rocketmq.sendMessageWithVIPChannel=false" -t styletang/rocketmq-console-ng
        
    -----------------------------至此,docker中安装rocketmq已经完成---------------------    
    访问 ip:8180就可以访问控制台
    
    
    问题解决：
        如果第2步骤出错：
            OpenJDK 64-Bit Server VM warning: Using the DefNew young collector with the CMS collector is deprecated and will likely be removed in a future release
            OpenJDK 64-Bit Server VM warning: UseCMSCompactAtFullCollection is deprecated and will likely be removed in a future release.
            OpenJDK 64-Bit Server VM warning: INFO: os::commit_memory(0x00000006ec800000, 2147483648, 0) failed; error='Cannot allocate memory' (errno=12)
            #
            # There is insufficient memory for the Java Runtime Environment to continue.
            # Native memory allocation (mmap) failed to map 2147483648 bytes for committing reserved memory.
            # An error report file with more information is saved as:
            # /opt/rocketmq-4.3.2/bin/hs_err_pid9.log
            
        这说明你的服务器内存不足了。
        
     