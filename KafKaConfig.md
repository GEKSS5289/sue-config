# 🛰Kafka_2.12-2.1.0
## 🧭安装简介
> ### 安装环境:CentOS 7
> ### 🚬下载
> ### 📦解压
> ### 🛠配置
> ### ⚗测试️
> ### 💿安装kafka管理工具
# 🚬下载Kafka_2.12-2.1.0
 ###  📞官方提供:[kafka_2.12-2.1.0](http://kafka.apache.org/downloads)
 ###  🤝作者提供:[kafka_2.12-2.1.0](https://shushun.oss-cn-shenzhen.aliyuncs.com/software/kafka_2.12-2.1.0.tgz)
# 📦解压
    tar -zxvf kafka_2.12-2.1.0.tar
    重命名: mv kafka_2.12-2.1.0 kafka
    
# 🛠配置 
    修改环境变量:
        cd /usr/local/kafka/config
        vim server.properties
            broker.id=0
            port=9092
            host.name=192.168.182.150
            advertised.host.name=192.168.182.150
            log.dirs=/usr/local/kafka/kafka-logs
            num.partitions=5
            zookeeper.connect=192.168.182.150:221,.......
    创建kafka存储消息(log日志数据)的目录
        mkdir /usr/local/kafka/kafka-logs
   

 # ⚗测试  
     启动:/usr/local/kafka/config/kafka-server-start.sh /usr/local/kafka/config/server.properties &
 # 💿安装kafka管理工具
     unzip kafka-manager-2.0.0.2.zip -d /usr/local/
     cd /usr/local
     mv kafka-manager-2.0.0.2 kafka-manager
 # ✏修改配置文件
     vim /usr/local/kafka-manager/conf/application.conf
        kafka-manager.zkhosts="192.168.182.150:2181,192.168.182.151:2181,192.168.182.152:2181"
 # 🥂启动kafka manager控制台
     /usr/local/kafka-manager-2.0.0.2/bin/kafka-manager &
     http://192.168.182.150:9000/
 # 🛰发送消息测试
      消费发送与接收验证
      cd /usr/local/kafka_2.12/bin
      启动发送消息的脚本
      ##--broker-list 192.168.11.221 指的是kafka  broker的地址列表
      ##--topic test 指的是把消息发送到test主题
      kafka-console-producer.sh --broker-list 192.168.11.221:2181 --topic test
      
      ## 启动接收消息的脚本
      kafka-console-consumer.sh --bootstrap-server 192.168.11.221:9092 --topic test