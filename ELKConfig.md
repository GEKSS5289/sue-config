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
            
# 👍关于watch警告
    ## 创建一个watcher,比如定义一个trigger 每个10s钟看一下input里的数据
    ## 创建一个watcher,比如定义一个trigger 每个5s钟看一下input里的数据
    PUT _xpack/watcher/watch/error_log_collector_watcher
    {
      "trigger": {
        "schedule": {
          "interval": "5s"
        }
      },
      "input": {
        "search": {
          "request": {
            "indices": ["<error_log_collector-{now+8h/d}>"],
            "body": {
              "size": 0,
              "query": {
                "bool": {
                  "must": [
                      {
                        "term": {"level": "ERROR"}
                      }
                  ],
                  "filter": {
                    "range": {
                        "currentDateTime": {
                        "gt": "now-30s" , "lt": "now"
                      }
                    }
                  } 
                }
              }
            }
          }
        }
      },
    
      "condition": {
        "compare": {
          "ctx.payload.hits.total": {
            "gt": 0
          }
        }
      },
     
      "transform": {
        "search": {
          "request": {
            "indices": ["<error-log-collector-{now+8h/d}>"],
            "body": {
              "size": 1,
              "query": {
                "bool": {
                  "must": [
                      {
                        "term": {"level": "ERROR"}
                      }
                  ],
                  "filter": {
                    "range": {
                        "currentDateTime": {
                        "gt": "now-30s" , "lt": "now"
                      }
                    }
                  } 
                }
              },
              "sort": [
                {
                    "currentDateTime": {
                        "order": "desc"
                    }
                }
              ]
            }
          }
        }
      },
      "actions": {
        "test_error": {
          "webhook" : {
            "method" : "POST",
            "url" : "http://192.168.11.31:8001/accurateWatch",
            "body" : "{\"title\": \"异常错误告警\", \"applicationName\": \"{{#ctx.payload.hits.hits}}{{_source.applicationName}}{{/ctx.payload.hits.hits}}\", \"level\":\"告警级别P1\", \"body\": \"{{#ctx.payload.hits.hits}}{{_source.messageInfo}}{{/ctx.payload.hits.hits}}\", \"executionTime\": \"{{#ctx.payload.hits.hits}}{{_source.currentDateTime}}{{/ctx.payload.hits.hits}}\"}"
          }
        }
     }
    }
    
    # 查看一个watcher
    # 
    GET _xpack/watcher/watch/error_log_collector_watcher
    
    
    #删除一个watcher
    DELETE _xpack/watcher/watch/error_log_collector_watcher
    
    #执行watcher
    # POST _xpack/watcher/watch/error_log_collector_watcher/_execute
    
    #查看执行结果
    GET /.watcher-history*/_search?pretty
    {
      "sort" : [
        { "result.execution_time" : "desc" }
      ],
      "query": {
        "match": {
          "watch_id": "error_log_collector_watcher"
        }
      }
    }
    
    GET error-log-collector-2019.09.18/_search?size=10
    {
    
      "query": {
        "match": {
          "level": "ERROR"
        }
      }
      ,
      "sort": [
        {
            "currentDateTime": {
                "order": "desc"
            }
        }
      ] 
    }
    
    
    GET error-log-collector-2019.09.18/_search?size=10
    {
    
      "query": {
        "match": {
          "level": "ERROR"
        }
      }
      ,
      "sort": [
        {
            "currentDateTime": {
                "order": "desc"
            }
        }
      ] 
    } 
# 🛴watch基础语法
    ## 创建一个watcher,比如定义一个trigger 每个10s钟看一下input里的数据
    PUT _xpack/watcher/watch/school_watcher
    {
      "trigger": {
        "schedule": {
          "interval": "10s"
        }
      },
      ## 查看任务信息
      "input": {
        "search": {
          "request": {
            ## 监控具体索引
            "indices": ["school*"],
            ## body里面具体些搜索语句
            "body": {
              "size": 0,
              "query": {
                "match": {
                  ## 比如索引里面name 有 hello 则进行报警
                  "name": "hello"
                }
              }
            }
          }
        }
      },
      ## 对于上面的查询结果进行比较：
      "condition": {
        ## compare进行比较
        "compare": {
          ## 上面的query查询的结果会放入到ctx.payload中：
          ## 比如获取 ctx.payload.hits.total  ctx.payload._shards.total 等等
          "ctx.payload.hits.total": {
            "gt": 0
          }
        }
      },
      ## transform作用：重新查询出文档内容赋值给ctx.payload
      "transform": {
        "search": {
          "request": {
            "indices": ["school*"],
            "body": {
              "size": 10,
              "query": {
                "match": {
                  "name": "hello"
                }
              }
            }
          }
        }
      },
      ## 根据上面的查询、比较结果，执行actions里面定义的动作（定义多种报警类型）
      "actions": {
        ## 报警名字
        "log_hello": {
          ## 防止报警风暴： 设置阈值 15m内曾经报警过, 则不报警
          "throttle_period": "15m",
          ## 报警方式：logging、mail、http等
          "logging": {
            ## 报警具体内容：使用 {{ 查询参数 }} 进行赋值：
            "text": "Found {{ctx.payload.hits.total}} hello in the school"
          }
        }
      }
    }
# ctx.payload取值范围
    ## payload取值规范：比如我们进行search搜索school：
    GET school/_search
    {
      "query": {
        "match": {
          "name": "zhangsan"
        }
      }
    }
    ##查询结果如下:
    {
      "took": 14,
      "timed_out": false,
      "_shards": {
        "total": 2,
        "successful": 2,
        "skipped": 0,
        "failed": 0
      },
      "hits": {
        "total": 1,
        "max_score": 1.5404451,
        "hits": [
          {
            "_index": "school",
            "_type": "student",
            "_id": "1",
            "_score": 1.5404451,
            "_source": {
              "name": "zhangsan",
              "age": 25,
              "course": "elasticsearch",
              "study_date": "2018-06-15T20:30:50",
              "mark": "today is a good day"
            }
          }
        ]
      }
    }
# 表示查询: ctx.payload结果集:
    ## 表示查询：ctx.payload结果集：
    {{#ctx.payload.hits.hits}} {{_source.name}} {{_source.course}} {{/ctx.payload.hits.hits}}
    比如我们进行search搜索school并采用聚合的方式来查询terms course数据:
    GET school/_search
    {
      "size": 0, 
      "aggs": {
        "myterms": {
          "terms": {
            "field": "course",
            "size": 10
          }
        }
      }
    }
# 查询结果：

{
  "took": 11,
  "timed_out": false,
  "_shards": {
    "total": 2,
    "successful": 2,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": 10,
    "max_score": 0,
    "hits": []
  },
  "aggregations": {
    "myterms": {
      "doc_count_error_upper_bound": 0,
      "sum_other_doc_count": 0,
      "buckets": [
        {
          "key": "elasticsearch",
          "doc_count": 7
        },
        {
          "key": "good",
          "doc_count": 1
        },
        {
          "key": "spring",
          "doc_count": 1
        },
        {
          "key": "spring elasticsearch",
          "doc_count": 1
        }
      ]
    }
  }
}
payload取值使用：现在想取得上面的hits.hits里面的数据内容，就可以使用如下方式：

# 表示查询：ctx.payload结果集：
    {{#ctx.payload.aggregations.aggsname.buckets}} {{key}} {{doc_count}} {{/ctx.payload.aggregations.aggsname.buckets}}

# 针对这里内容就是：
    {{#ctx.payload.aggregations.myterms.buckets}} {{key}} {{doc_count}} {{/ctx.payload.aggregations.myterms.buckets}}
# watcher API使用：
## 查看一个watcher
    GET _xpack/watcher/watch/school_watcher

## 删除一个watcher
    DELETE _xpack/watcher/watch/school_watcher

# 执行watcher
    POST _xpack/watcher/watch/school_watcher/_execute

# 查看执行结果
    GET /.watcher-history*/_search?pretty
    {
      "sort" : [
        { "result.execution_time" : "desc" }
      ],
      "query": {
        "match": {
          "watch_id": "school_watcher"
        }
      }
    }
# triggers的几种类型
       #--------------------triggers的几种类型--------------------
       #hourly、daily、weekly、monthly、yearly、cron、interval
       
       #hourly：每小时执行
       #例如：12:00, 12:15, 12:30, 12:45, 1:00, 1:15
       {
         "trigger" : {
           "schedule" : {
             "hourly" : { "minute" : [ 0, 15, 30, 45 ] }
           }
         }
       }
       
       #daily：每天执行
       #每天00:00, 12:00, and 17:00
       {
         "trigger" : {
           "schedule" : {
             "daily" : { "at" : [ "midnight", "noon", "17:00" ] }
           }
         }
       }
       #每天00:00, 00:30, 12:00, 12:30, 17:00 and 17:30
       {
         "trigger" : {
           "schedule" : {
             "daily" : {
               "at" {
                 "hour" : [ 0, 12, 17 ],
                 "minute" : [0, 30]
               }
             }
           }
         }
       }
       
       #weekly：指定星期几
       #周二12:00,周五17:00
       {
         "trigger" : {
           "schedule" : {
             "weekly" : [
               { "on" : "tuesday", "at" : "noon" },
               { "on" : "friday", "at" : "17:00" }
             ]
           }
         }
       }
       #周二、周五的17：00
       {
         "trigger" : {
           "schedule" : {
             "weekly" : {
               "on" : [ "tuesday", "friday" ],
               "at" : [ "noon", "17:00" ]
             }
           }
         }
       }
       
       #monthly：指定每月哪天执行
       #每月10号中午、每月20号17:00
       {
         "trigger" : {
           "schedule" : {
             "monthly" : [
               { "on" : 10, "at" : "noon" },
               { "on" : 20, "at" : "17:00" }
             ]
           }
         }
       }
       #每月10号、20号的00:00,12:00
       {
         "trigger" : {
           "schedule" : {
             "monthly" : {
               "on" : [ 10, 20 ],
               "at" : [ "midnight", "noon" ]
             }
           }
         }
       }
       #yearly-指定月、日、时
       #每年的1月10日12:00,每年的7月20日17:00
       {
         "trigger" : {
           "schedule" : {
             "yearly" : [
               { "in" : "january", "on" : 10, "at" : "noon" },
               { "in" : "july", "on" : 20, "at" : "17:00" }
             ]
           }
         }
       }
       #每年1月10日，1月20日，12月10日，12月20日的12:00,00:00
       {
         "trigger" : {
           "schedule" : {
             "yearly" : {
               "in" : [ "jan", "dec" ],
               "on" : [ 10, 20 ],
               "at" : [ "midnight", "noon" ]
             }
           }
         }
       }
       #cron-表达式
       <seconds> <minutes> <hours> <day_of_month> <month> <day_of_week> [year]
       0 5 9 * * ?
       0 0-5 9 * * ?
       0 0/15 9 * * ?
       
       #interval-周期的
       #间隔单位：s:秒、m:分钟、h:小时、d:天、w:星期

input的几种类型：

       #--------------------Inputs的几种类型--------------------
       #Simple、Search、HTTP、Chain
       
       #Simple Input-静态数据
       #每天12点触发
       {
         "trigger" : {
           "schedule" : {
             "daily" : { "at" : "noon" }
           }
         },
         "input" : {
           "simple" : {
             "name" : "John"
           }
         },
         "actions" : {
           "reminder_email" : {
             "email" : {
               "to" : "to@host.domain",
               "subject" : "Reminder",
               "body" : "Dear {{ctx.payload.name}}, by the time you read these lines, I'll be gone"
             }
           }
         }
       }
       
       
       #Search-搜索
       {
         "input" : {
           "search" : {
             "request" : {
               "indices" : [ "logs" ],
               "body" : {
                 "query" : { "match_all" : {} }
               }
             }
           }
         },
         "condition" : {
           "compare" : { "ctx.payload.hits.total" : { "gt" : 5 }}
         }
         ...
       }
       
    #Http-请求
    #request.host
    #request.port
    #request.path
    #request.headers
    #request.params
    #request.url：request.scheme, request.host, request.port and request.params
    #request.method：head、get、post、put、delete
    #request.auth
    #request.body
    #request.proxy.host
    #request.proxy.port
    #request.connection_timeout
    #request.read_timeout
    #response_content_type：json, yaml and text
    #extract
    #get请求
       {
        "input" : {
          "http" : {
            "request" : {
              "host" : "example.com",
              "port" : 9200,
              "path" : "/idx/_search"
            }
          }
        }
       }
   
# 含有body体内容
       {
        "input" : {
            "http" : {
                "request" : {
                  "host" : "host.domain",
                  "port" : 9200,
                  "path" : "/idx/_search",
                  "body" :  "{\"query\" :  {  \"match\" : { \"category\" : \"event\"}}}"
                }
            }
        }
       }
   
# 含有参数的
       {
        "input" : {
          "http" : {
            "request" : {
              "host" : "host.domain",
              "port" : "9200",
              "path" : "/_cluster/stats",
              "params" : {
                "human" : "true" 
              }
            }
          }
        }
       }

# 含有用户密码
       {
        "input" : {
          "http" : {
            "request" : {
              "host" : "host.domain",
              "port" : "9200",
              "path" : "/myservice",
              "auth" : {
                "basic" : {
                  "username" : "user",
                  "password" : "pass"
                }
              }
            }
          }
        }
       }
   
# 直接请求url的
       {
        "input" : {
          "http" : {
            "request" : {
              "url" : "http://api.openweathermap.org/data/2.5/weather",
              "params" : {
                "lat" : "52.374031",
                "lon" : "4.88969",
                "appid" : "<your openweathermap appid>"
              }
            }
          }
        }
       }
   
# Chain-input-同时设置多个input，串行
       {
        "input" : {
          "chain" : {
            "inputs" : [ 
              ## 第一步input
              {
                "first" : {
                  "simple" : { "path" : "/_search" }
                }
              },
              ## 第二步input （可以去使用第一步input返回的结果）
              {
                "second" : {
                  "http" : {
                    "request" : {
                      "host" : "localhost",
                      "port" : 9200,
                      "path" : "{{ctx.payload.first.path}}" 
                    }
                  }
                }
              }
            ]
          }
        }
       }

    condition条件设置：如果condition条件返回true 则会触发action 如果返回 false 则就停止，不执行action

# --------------------条件设置--------------------
    #Always Condition
    "condition" : {
         "always" : {}
    }
    #Never Condition
       "condition" : {
         "never" : {}
    }
    
    
    #Compare Condition （进行和查询的结果进行比较语法如下：）
    # eq:、not_eq、gt、gte、lt、lte
    ## 比如错误条数超过了5条进行报警、响应长时间超过多少毫秒进行报警等
    {
         "condition" : {
           "compare" : {
             "ctx.payload.hits.total" : { 
               "gte" : 5 
             }
         }
    }
    
    #<{expression}> 正则表达式 使用 <> 中写正则表达式： 比如 当前时间 - 5分钟 进行比较，如下：
    {
         "condition" : {
           "compare" : {
             "ctx.execution_time" : {
               "gte" : "<{now-5m}>"
             }
         }
    }
    
    
    #{{path}} 比较，这个就是最开始的示例里面的获取参数方式，如下：
    {
         "condition" : {
           "compare" : {
             "ctx.payload.aggregations.status.buckets.error.doc_count" : {
               "not_eq" : "{{ctx.payload.aggregations.handled.buckets.true.doc_count}}"
             }
         }
    }
       
      
    #Array Compare Condition 数组比较： 比如当前的doc_count大于25 就进行报警
    {
         "condition": {
           "array_compare": {
             "ctx.payload.aggregations.top_tweeters.buckets" : { 
               "path": "doc_count" ,
               "gte": { 
                 "value": 25, 
               }
             }
           }
         }
    }
       
    #Script Condition 脚本方式
    {
         "input" : {
           "search" : {
             "indices" : "log-events",
             "body" : {
               "size" : 0,
               "query" : { "match" : { "status" : "error" } }
             }
           }
         },
         "condition" : {
           "script" : {
             ## 当前返回的条数是否大于阈值，进行报警
             "inline" : "return ctx.payload.hits.total > threshold",
             "params" : {
               "threshold" : 5
             }
           }
         }
    }

# Action 触发器

# --------------------Actions--------------------
    #Email Action--发送邮件 
    
    #如果使用发送邮件的报警，则需要在elasticsearch.yml中配置发送邮件服务的信息
    xpack.notification.email:
       default_account: gmail_account
       account:
         gmail_account:
             profile: gmail
             smtp:
                 auth: true
                 starttls.enable: true
                 host: smtp.gmail.com
                 port: 587
                 user: <username>
                 password: <password>
         outlook_account:
             profile: outlook
             smtp:
                 auth: true
                 starttls.enable: true
                 host: smtp-mail.outlook.com
                 port: 587
                 user: <username>
                 password: <password>:
        exchange_account:
             profile: outlook
             email_defaults:
                 from: <email address of service account> 
             smtp:
                 auth: true
                 starttls.enable: true
                 host: <your exchange server>
                 port: 587
                 user: <email address of service account> 
                 password: <password>
   
# 发送邮件
    "actions" : {
         ## actions名字
         "send_email" : { 
           "email" : { 
             "to" : "'Recipient Name <recipient@example.com>'", 
             #"to" : ['Personal Name <user1@host.domain>', 'user2@host.domain'], 
             "subject" : "Watcher Notification", 
             "body" : "{{ctx.payload.hits.total}} error logs found" 
           }
         }
    }

# 发送含有附件信息的邮件
    "actions" : {
         "email_admin" : {
           "email": {
             "to": "'John Doe <john.doe@example.com>'",
             "attachments" : {
               ## 附件方式
               "my_image.png" : { 
                 "http" : { 
                   "content_type" : "image.png",
                   "request" : {
                     "url": "http://example.org/foo/my-image.png" 
                   }
                 }
               },
               ## xpack reporting插件生成方式：
               "dashboard.pdf" : {
                 "reporting" : {
                   "url": "http://example.org:5601/api/reporting/generate/dashboard/Error-Monitoring"
                 }
               },
               ## 自定义附件
               "data.yml" : {
                 "data" : {
                   "format" : "yaml" 
                 }
               }
             }
           }
         }
    }

# Webhook Action，发送一个http请求
    #发送github的issue
    "actions" : {
         "create_github_issue" : {
           ## 因为发邮件到达率不是特别高，所以可以使用外部的接口调用方式
           ## 比如这里调用url为外部的手机短信接口进行发送 
           "webhook" : {
             ## 请求方式
             "method" : "POST",
             ## 外部请求地址
             "url" : "https://api.github.com/repos/<owner>/<repo>/issues",
             ## 请求报文
             "body" : "{
               \"title\": \"Found errors in 'contact.html'\",
               \"body\": \"Found {{ctx.payload.hits.total}} errors in the last 5 minutes\",
               \"assignee\": \"web-admin\",
               \"labels\": [ \"bug\", \"sev2\" ]
             }",
             ## 用户名密码
             "auth" : {
               "basic" : {
                 "username" : "<username>", 
                 "password" : "<password>"
               }
             }
           }
         }
    }
    
    #带有url参数的请求
    "actions" : {
         "my_webhook" : {
           "webhook" : {
             "method" : "POST",
             "host" : "mylisteningserver",
             "port" : 9200,
             "path": ":/alert",
             "params" : {
               "watch_id" : "{{ctx.watch_id}}" 
             }
           }
         }
    }
    
    #自定义header
    "actions" : {
         "my_webhook" : {
           "webhook" : {
             "method" : "POST",
             "host" : "mylisteningserver",
             "port" : 9200,
             "path": ":/alert/{{ctx.watch_id}}",
             "headers" : {
               "Content-Type" : "application/yaml" 
             },
             "body" : "count: {{ctx.payload.hits.total}}"
           }
         }
    }
       
    #Index Action--创建索引文档
    "actions" : {
         "index_payload" : { 
           "index" : {
             "index" : "my-index", 
             "doc_type" : "my-type", 
             "doc_id": "my-id" 
           }
         }
    }
       
# Logging Action--记录日志
    # level：error, warn, info, debug and trace
    ## 日志种类：
    # category：xpack.watcher.actions.logging
    "actions" : {
         "log" : { 
           "transform" : { ... }, 
           ## 日志报警
           "logging" : {
             "text" : "executed at {{ctx.execution_time}}",
             ## 日志级别
             "level": "info"
           }
         }
    }

# Jira Action 与jira集成
    #HipChat Action
    #Slack Action
    #PagerDuty Action
    
    使用接口的形式创建一个watcher, 进行模拟：
    
    执watcher脚本：
    
    ## 查询school
    GET school/student/_search
    {
      "query": {
        "match_all":{}
      }
    }
    
    ## 创建school_watcher
    PUT _xpack/watcher/watch/school_watcher
    {
      "trigger": {
        "schedule": {
          "interval": "10s"
        }
      },
      "input": {
        "search": {
          "request": {
            "indices": ["school*"],
            "body": {
              "size": 0,
              "query": {
                "match": {
                  "name": "hello"
                }
              }
            }
          }
        }
      },
      "condition": {
        "compare": {
          "ctx.payload.hits.total": {
            "gt": 0
          }
        }
      },
      "transform": {
        "search": {
          "request": {
            "indices": ["school*"],
            "body": {
              "size": 10,
              "query": {
                "match": {
                  "name": "hello"
                }
              }
            }
          }
        }
      },
      "actions": {
        "log_hello": {
          "throttle_period": "15m",
          "logging": {
            "text": "Found {{ctx.payload.hits.total}} hello in the school"
          }
        }
      }
    }

# 查看watcher执行结果
    GET /.watcher-history*/_search?pretty
    {
      "sort" : [
        { "result.execution_time" : "desc" }
      ],
      "query": {
        "match": {
          "watch_id": "school_watcher"
        }
      }
    }

# 进行数据测试：
    POST /school/student
    {
     "name": "hello",
     "age": 18,
     "course": "elasticsearch",
     "study_date": "2018-08-20T20:30:50",
     "mark": "take care day day"
    }


    可视化操作watcher，可以启用、禁用、添加修改、删除watcher

     ## watch使用文章：https://www.cnblogs.com/reboot51/p/8328720.html

# watcher使用：
    ## 创建一个watcher,比如定义一个trigger 每个10s钟看一下input里的数据
    PUT _xpack/watcher/watch/applog_error_watcher
    {
      "trigger": {
        "schedule": {
          "interval": "10s"
        }
      },
      "input": {
        "search": {
          "request": {
            "indices": ["javalog-app-*"],
            "body": {
              "size": 0,
              "query": {
                "match": {
                  "level": "ERROR"
                }
              }
            }
          }
        }
      },
      "condition": {
        "compare": {
          "ctx.payload.hits.total": {
            "gt": 0
          }
        }
      },
      "transform": {
        "search": {
          "request": {
            "indices": ["javalog-app-*"],
            "body": {
              "size": 10,
              "query": {
                "match": {
                  "name": "hello"
                }
              }
            }
          }
        }
      },
      "actions": {
        "test_error": {
        "throttle_period": "1m",
        "webhook" : {
          "method" : "POST",
          "url" : "http://192.168.11.32:8001/watch",
          "body" : "{
            \"title\": \"异常错误告警\",
            \"traceId\": \"{{#ctx.payload.hits.hits}} {{_source.traceId}} {{/ctx.payload.hits.hits}}\", 
            \"spanId\" : \"{{#ctx.payload.hits.hits}} {{_source.spanId}} {{/ctx.payload.hits.hits}}\",
            \"level\":\"告警级别P1\",
            \"body\": \"{{#ctx.payload.hits.hits}} {{_source.messageInfo}} {{/ctx.payload.hits.hits}}\"
           }
          }
        }
     }
    }
    
    
    
    
    
    ## {{#ctx.payload.hits.hits}} {{_source.traceId}} {{/ctx.payload.hits.hits}}
    ## {{#ctx.payload.hits.hits}} {{_source.spanId}} {{/ctx.payload.hits.hits}}
    ## {{#ctx.payload.hits.hits}} {{_source.messageInfo}} {{/ctx.payload.hits.hits}}
    
    
    ## 查询error
    GET javalog-app-2019.01.24/_search
    {
      "query": {
        "match": {
          "level.keyword": "ERROR"
        }
      }
    }
    
    
    # 查看一个watcher
    # GET _xpack/watcher/watch/applog_error_watcher
    
    #删除一个watcher
    # DELETE _xpack/watcher/watch/applog_error_watcher
    
    #执行watcher
    # POST _xpack/watcher/watch/applog_error_watcher/_execute
    
    #查看执行结果
    GET /.watcher-history*/_search?pretty
    {
      "sort" : [
        { "result.execution_time" : "desc" }
      ],
      "query": {
        "match": {
          "watch_id": "applog_error_watcher"
        }
      }
    }
