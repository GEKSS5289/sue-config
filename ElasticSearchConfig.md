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