# 🔍ElasticSearch-7.2.4(CentOS 7)
## 安装简介
   - 🚬下载ElasticSearch-7.2.4
   - 📦解压
   - 🛠配置
   - 🍻启动
# 🚬下载ElasticSearch-7.2.4
  ### 📞官方提供:[ElasticSearch](https://www.baidu.com/link?url=BO9hMQut-GAg1T7zmYqErWcejyIBEyuaR4CNv7h0fWpheNnTHPPz0Q__u5aOFzeI&wd=&eqid=831f522800019878000000065f322b27)
  ### 🤝作者提供:[ElasticSearch](https://shushun.oss-cn-shenzhen.aliyuncs.com/software/elasticsearch-7.4.2-linux-x86_64.tar.gz)  
# 📦解压
        tar -zxvf elasticsearch-7.4.2-linux-x86_64.tar.gz -C /usr/local/
# 🛠配置
   ## 🚩进入elasticsearch-7.2.4文件夹
   ##### 📗目录介绍:
                bin:可执行文件在里面，运行es的命令就在这个里面，包含了一些脚本文件等
                config:配置文件目录
                JDK:java环境
                lib:依赖的jar，类库
                logs:日志文件
                modules:es相关的模块
                plugins:可以自己开发的插件
                data:这个目录没有，自己新建一下，后面要用 -> mkdir data，这个作为索引目录
   ##### ✏修改config文件夹里的elasticearch.yml
                vim elasticearch.yml
                    cluster.name: sue-elasticsearch
                    node.name: es-node1
                    path.data: /usr/local/elasticsearch-7.2.4/data
                    path.logs: /usr/local/elasticsearch-7.2.4/logs
                    network.host: 0.0.0.0
                    cluster.initial_master_nodes: ["es-node1"]
   ##### ✏修改config文件夹里的jvm.options
                vim jvm.options
                    -Xms128m
                    -Xmx128m
   ##### 👨添加用户(注意:因为es不允许root操作直接执行文件，所以需要创建新的es用户)
                useradd esuser
                chown -R esuser:esuser /usr/local/elasticsearch-7.2.4
                su esuser
   ##### ✏修改/etc/security/limits.conf
                vim /etc/security/limits.conf
                    * soft nofile 65536
                    * hard nofile 131072
                    * soft nproc 2048
                    * hard nproc 4096 
   ##### ✏修改/etc/sysctl.conf
                vim /etc/sysctl.conf
                    vm.max_map_count=262145
                sysctl -p
        
# 🍻启动
        cd /usr/local/elasticsearch/bin
        ./elasticsearch -d (后台运行模式)  
        http://ip:9200 (测试是否成功)
# 🌈成功
        {
            "name": "es-node1",
            "cluster_name": "sue-elasticsearch",
            "cluster_uuid": "_zvcH0v-SOebVzhhmMbbJw",
            "version": {
            "number": "7.4.2",
            "build_flavor": "default",
            "build_type": "tar",
            "build_hash": "2f90bbf7b93631e52bafb59b3b049cb44ec25e96",
            "build_date": "2019-10-28T20:40:44.881551Z",
            "build_snapshot": false,
            "lucene_version": "8.2.0",
            "minimum_wire_compatibility_version": "6.8.0",
            "minimum_index_compatibility_version": "6.0.0-beta1"
            },
            "tagline": "You Know, for Search"
        }         
# 🔍ElasticSearch-7.2.4集群搭建
  ## 😱为什么需要搭建集群?
       😁单机es可以用，没毛病，但是有一点我们需要去注意，就是高可用是需要关注的
       一般我们可以把es搭建成集群，2台以上就能成为es集群了。
       集群不仅可以实现高可用，也能实现海量数据存储的横向扩展。
  ## ⌚分片机制
  #### 每个索引可以被分片，就相当于吃披萨的时候被切了好几块，然后分给不同的人吃
       索引my_doc只有一个主分片；
       索引shop有3个主分片；
       索引shop2有5个主分片。
       每个主分片都包含索引的数据，由于目前是单机，所以副本分片是没有的，这个时候集群健康值显示为黄色。
       副本分片是主分片的备份，主挂了，备份还是可以访问，这就需要用到集群了。
       同一个分片的主与副本是不会放在同一个服务器里的，因为一旦宕机，这个分片就没了
  ## 🚗es集群搭建的准备工作
       使用虚拟机克隆功能，把单个es的虚拟机克隆为3个，我们会搭建3节点的es集群。
  ## ☝Show
       将三台ElasticSearch节点上的/usr/local/elasticsearch/data下的nodes文件夹删除
  ## ⚙配置集群(修改/usr/local/elasticsearch/config/elasticsearch.yml)
       # 配置集群名称，保证每个节点的名称相同，如此就能都处于一个集群之内了
       cluster.name: imooc-es-cluster
       
       # 每一个节点的名称，必须不一样
       node.name: es-node1
       
       # http端口（使用默认即可）
       http.port: 9200
       
       # 主节点，作用主要是用于来管理整个集群，负责创建或删除索引，管理其他非master节点（相当于企业老总）
       node.master: true
       
       # 数据节点，用于对文档数据的增删改查
       node.data: true
       
       # 集群列表
       discovery.seed_hosts: ["192.168.1.184", "192.168.1.185", "192.168.1.186"]
       
       # 启动的时候使用一个master节点
       cluster.initial_master_nodes: ["es-node1"]
       # 通用配置
       http.cors.enabled: true
       http.cors.allow-origin: "*"
       network.publish_host: 192.168.182.150(本机地址)
  ## 🔍查看配置内容
       more elasticsearch.yml | grep ^[^#]
  ## 🍻启动(三台服务器)
       cd /usr/local/elasticsearch/bin
       ./elastsearch -d