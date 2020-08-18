# 📃FileBeat6.6.0
## 安装简介
   - 🚬下载FileBeat6.6.0
   - 📦解压
   - 🛠配置
   - 🍻启动
# 🚬下载FileBeat6.6.0
  ### 🤝作者提供:[FileBeat](https://shushun.oss-cn-shenzhen.aliyuncs.com/software/filebeat-6.6.0-linux-x86_64.tar.gz)  
# 📦解压
        tar -zxvf filebeat-6.6.0-linux-x86_64.tar.gz -C /usr/local/
        cd /usr/local
        mv filebeat-6.6.0-linux-x86_64/ filebeat-6.6.0
# 🛠配置
 
   ##### ✏修改filebeat.yml (将抓取到的信息发送到kafka)
             vim /usr/local/filebeat-5.6.2/filebeat.yml
                    ###################### Filebeat Configuration Example #########################
                    filebeat.prospectors:
                    
                    - input_type: log
                    
                      paths:
                        ## app-服务名称.log, 为什么写死，防止发生轮转抓取历史数据
                        - /usr/local/logs/app-collector.log
                      #定义写入 ES 时的 _type 值
                      document_type: "app-log"
                      multiline:
                        #pattern: '^\s*(\d{4}|\d{2})\-(\d{2}|[a-zA-Z]{3})\-(\d{2}|\d{4})'   # 指定匹配的表达式（匹配以 2017-11-15 08:04:23:889 时间格式开头的字符串）
                        pattern: '^\['                              # 指定匹配的表达式（匹配以 "{ 开头的字符串）
                        negate: true                                # 是否匹配到
                        match: after                                # 合并到上一行的末尾
                        max_lines: 2000                             # 最大的行数
                        timeout: 2s                                 # 如果在规定时间没有新的日志事件就不等待后面的日志
                      fields:
                        logbiz: collector
                        logtopic: app-log-collector   ## 按服务划分用作kafka topic
                        evn: dev
                    
                    - input_type: log
                    
                      paths:
                        - /usr/local/logs/error-collector.log
                      document_type: "error-log"
                      multiline:
                        #pattern: '^\s*(\d{4}|\d{2})\-(\d{2}|[a-zA-Z]{3})\-(\d{2}|\d{4})'   # 指定匹配的表达式（匹配以 2017-11-15 08:04:23:889 时间格式开头的字符串）
                        pattern: '^\['                              # 指定匹配的表达式（匹配以 "{ 开头的字符串）
                        negate: true                                # 是否匹配到
                        match: after                                # 合并到上一行的末尾
                        max_lines: 2000                             # 最大的行数
                        timeout: 2s                                 # 如果在规定时间没有新的日志事件就不等待后面的日志
                      fields:
                        logbiz: collector
                        logtopic: error-log-collector   ## 按服务划分用作kafka topic
                        evn: dev
                        
                    output.kafka:
                      enabled: true
                      hosts: ["192.168.182.151:9092"]
                      topic: '%{[fields.logtopic]}'
                      partition.hash:
                        reachable_only: true
                      compression: gzip
                      max_message_bytes: 1000000
                      required_acks: 1
                    logging.to_files: true
        
# 🍻启动
        
        cd /usr/local/filebeat-6.6.0
        检查配置文件:./filebeat -c filebeat.yml -configtest 
        /usr/local/filebeat-6.6.0/filebeat &
# 🌈成功
      ps -ef | grep filebeat
      能够看到filebeat相应进程