# 📃ELK6.6.0
> 👍勿谓言之不预:此场景用作海量日志收集 
> kafka作为数据中转 web应用log4j12插件生成的日志文件将会被filebeat抓取
> 随后发送到kafka中去 则filebeat属于消息生产者
> 在由logstatsh从kafka中得到由filebeat发送的日志消息 则logstatsh为消费者
> logstatsh将得到的日志消息 以管道的方式丢给Elasticsearch做日志搜索
> Kibana为标准的视图界面 将Elasticsearch搜索到结果显示到此
## 安装简介
> ### 🛠搭建kafka环境
> ### 🚬下载FileBeat6.6.0
> ### 🚬下载Logstatsh6.6.0
> ### 🚬下载ElasticSearch6.6.0
> ### 🚬下载Kibana6.6.0
> ### 😜配置
> ### 🍪启动
# 🛠搭建kafka环境
  > ### 🛰Kafka配置->[`KafkaConfig.md`](https://github.com/GEKSS5289/sue-config/blob/master/KafKaConfig.md)
# 🚬下载集合
  ### 🤝作者提供:[ElasticSearch](https://shushun.oss-cn-shenzhen.aliyuncs.com/software/elasticsearch-6.6.0.tar.gz)  
  ### 🤝作者提供:[FileBeat](https://shushun.oss-cn-shenzhen.aliyuncs.com/software/filebeat-6.6.0-linux-x86_64.tar.gz) 
  ### 🤝作者提供:[Kibana](https://shushun.oss-cn-shenzhen.aliyuncs.com/software/kibana-6.6.0-linux-x86_64.tar.gz) 
  ### 🤝作者提供:[Logstatsh](https://shushun.oss-cn-shenzhen.aliyuncs.com/software/logstash-6.6.0.tar.gz)
# 😜配置
  > ### 🛥FileBeat配置->[`FileBeatConfig.md`](https://github.com/GEKSS5289/sue-config/blob/master/FileBeatConfig.md) 
  > ### 🦾Logstatsh-6.4.3配置(6.6.0通用)->[`LogstatshConfig.md`](https://github.com/GEKSS5289/sue-config/blob/master/LogstatshConfig.md)
  > ### 🔍ElasticSearch-7.2.4配置(6.6.0通用)->[`ElasticSearchConfig.md`](https://github.com/GEKSS5289/sue-config/blob/master/ElasticSearchConfig.md)
  > ### 👘Kibana配置->[`KibanaConfig.md`](https://github.com/GEKSS5289/sue-config/blob/master/FileBeatConfig.md)
# 🛠配置
        关于logstatsh启动配置文件配置:
            logstatsh-db-sync.conf -> 重命名 logstash-script.conf
            内容全部替换:
                ## multiline 插件也可以用于其他类似的堆栈式信息，比如 linux 的内核日志。
                input {
                  kafka {
                    ## app-log-服务名称
                    topics_pattern => "app-log-.*"
                    bootstrap_servers => "192.168.182.151:9092"
                	codec => json
                	consumer_threads => 1	## 增加consumer的并行消费线程数
                	decorate_events => true
                    #auto_offset_rest => "latest"
                	group_id => "app-log-group"
                   }
                   
                   kafka {
                    ## error-log-服务名称
                    topics_pattern => "error-log-.*"
                    bootstrap_servers => "192.168.182.151:9092"
                	codec => json
                	consumer_threads => 1
                	decorate_events => true
                    #auto_offset_rest => "latest"
                	group_id => "error-log-group"
                   }
                   
                }
                
                filter {
                  
                  ## 时区转换
                  ruby {
                	code => "event.set('index_time',event.timestamp.time.localtime.strftime('%Y.%m.%d'))"
                  }
                
                  if "app-log" in [fields][logtopic]{
                    grok {
                        ## 表达式
                        match => ["message", "\[%{NOTSPACE:currentDateTime}\] \[%{NOTSPACE:level}\] \[%{NOTSPACE:thread-id}\] \[%{NOTSPACE:class}\] \[%{DATA:hostName}\] \[%{DATA:ip}\] \[%{DATA:applicationName}\] \[%{DATA:location}\] \[%{DATA:messageInfo}\] ## (\'\'|%{QUOTEDSTRING:throwable})"]
                    }
                  }
                
                  if "error-log" in [fields][logtopic]{
                    grok {
                        ## 表达式
                        match => ["message", "\[%{NOTSPACE:currentDateTime}\] \[%{NOTSPACE:level}\] \[%{NOTSPACE:thread-id}\] \[%{NOTSPACE:class}\] \[%{DATA:hostName}\] \[%{DATA:ip}\] \[%{DATA:applicationName}\] \[%{DATA:location}\] \[%{DATA:messageInfo}\] ## (\'\'|%{QUOTEDSTRING:throwable})"]
                    }
                  }
                  
                }
                
                ## 测试输出到控制台：
                output {
                  stdout { codec => rubydebug }
                }
                
                
                ## elasticsearch：
                output {
                
                  if "app-log" in [fields][logtopic]{
                	## es插件
                	elasticsearch {
                  	    # es服务地址
                        hosts => ["192.168.182.151:9200"]
                        # 用户名密码      
                        user => "elastic"
                        password => "123456"
                        ## 索引名，+ 号开头的，就会自动认为后面是时间格式：
                        ## javalog-app-service-2019.01.23 
                        index => "app-log-%{[fields][logbiz]}-%{index_time}"
                        # 是否嗅探集群ip：一般设置true；http://192.168.11.35:9200/_nodes/http?pretty
                        # 通过嗅探机制进行es集群负载均衡发日志消息
                        sniffing => true
                        # logstash默认自带一个mapping模板，进行模板覆盖
                        template_overwrite => true
                    } 
                  }
                  
                  if "error-log" in [fields][logtopic]{
                	elasticsearch {
                        hosts => ["192.168.182.151:9200"]    
                        user => "elastic"
                        password => "123456"
                        index => "error-log-%{[fields][logbiz]}-%{index_time}"
                        sniffing => true
                        template_overwrite => true
                    } 
                  }
                  
                
                }
           
        
# 🍪启动
       启动kafka:/usr/local/kafka/config/kafka-server-start.sh /usr/local/kafka/config/server.properties &
       创建ELK环境所需要的Topic:
            kafka-topics.sh --zookeeper 192.168.11.111:2181 --create --topic app-log-collector --partitions 1  --replication-factor 1
            kafka-topics.sh --zookeeper 192.168.11.111:2181 --create --topic error-log-collector --partitions 1  --replication-factor 1
       启动filebeat:
            /usr/local/filebeat-6.6.0/filebeat &
       启动Elasticsearch:
            cd /usr/local/elasticsearch/bin
            ./elasticsearch -d (后台运行模式)  
            http://ip:9200 (测试是否成功)
       启动Kibana:
            /usr/local/kibana-6.6.0/bin/kibana -c /usr/local/kibana-6.6.0/config/kibana.yml
       启动logstatsh:
            cd /logstash-6.6.0/bin
            ./logstatsh -f /usr/local/logstash-6.4.3/sync/logstash-script.conf 