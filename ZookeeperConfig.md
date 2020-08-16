# ☕Zookeeper-3.4.14
## 🧭安装简介
> ### 安装环境:CentOS 7
> ### 🚬下载
> ### 📦解压
> ### 🛠配置
> ### ⚗测试
> ### 🕹️Zookeeper开机自启动️
# 🚬下载Zookeeper-3.4.14
 ###  📞官方提供:[Zookeeper-3.4.14](https://www.apache.org/dyn/closer.lua/zookeeper/zookeeper-3.4.14/zookeeper-3.4.14.tar.gz)
 ###  🤝作者提供:[Zookeeper-3.4.14](https://www.shushunstudio.com/software/zookeeper-3.4.14.tar.gz)
# 📦解压
    tar -zxvf zookeeper-3.4.14.tar.gz
    重命名: mv zookeeper-3.4.6 zookeeper
    
# 🛠配置 
    修改环境变量:
        vim /etc/profile
            export ZOOKEEPER_HOME=/usr/local/zookeeper
            export PATH=.:$ZOOKEEPER_HOME/bin
            
            
    刷新环境变量:
        source /etc/profile
        
        
    zookeeper文件夹下修改配置文件
      首先到指定目录： cd /usr/local/zookeeper/conf
      重命名 mv zoo_sample.cfg zoo.cfg
      
      
      
    修改zoo.cfg
        dataDir=/usr/local/zookeeper/data
        server.0=bhz125:2888:3888
        server.1=bhz126:2888:3888
        server.2=bhz127:2888:3888
        
        
        
    增加服务器标识配置，需要2步骤，第一是创建文件夹和文件，第二是添加配置内容
        创建文件夹： mkdir /usr/local/zookeeper/data
        创建文件myid vim /usr/local/zookeeper/data/myid
        注意这里每一台服务器的myid文件内容不同，分别修改里面的值为0，1，2；
        与我们之前的zoo.cfg配置文件里：server.0，server.1，server.2 顺序相对应，然后保存退出；
 # ⚗测试  
    启动路径：/usr/local/zookeeper/bin（也可在任意目录，因为配置了环境变量）
    执行命令：zkServer.sh start (注意这里3台机器都要进行启动，启动之后可以查看状态)
    查看状态：zkServer.sh status (在三个节点上检验zk的mode, 会看到一个leader和俩个follower)
 # 👦Zookeeper客户端操作
    zkCli.sh 进入zookeeper客户端
    根据提示命令进行操作： 
    查找：ls /   ls /zookeeper
    创建并赋值： create /imooc zookeeper
    获取： get /imooc 
    设值： set /imooc zookeeper1314 
    PS1: 任意节点都可以看到zookeeper集群的数据一致性
    PS2: 创建节点有俩种类型：短暂（ephemeral） 持久（persistent）, 这些小伙伴们可以查找相关资料，我们这里作为入门不做过多赘述！
 # Zookeeper核心配置详解(zoo.cfg配置文件)
    tickTime：基本事件单元，以毫秒为单位。这个时间是作为 Zookeeper 服务器之间或客户端与服务器之间维持心跳的时间间隔，也就是每隔 tickTime时间就会发送一个心跳。
    				
    initLimit：这个配置项是用来配置 Zookeeper 接受客户端初始化连接时最长能忍受多少个心跳时间间隔数，当已经超过 10 个心跳的时间（也就是 tickTime）长度后 Zookeeper 服务器还没有收到客户端的返回信息，那么表明这个客户端连接失败。总的时间长度就是 10*2000=20 秒。
    		
    syncLimit：这个配置项标识 Leader 与 Follower 之间发送消息，请求和应答时间长度，最长不能超过多少个 tickTime 的时间长度，总的时间长度就是 5*2000=10 秒
    				
    dataDir：存储内存中数据库快照的位置，顾名思义就是 Zookeeper 保存数据的目录，默认情况下，Zookeeper 将写数据的日志文件也保存在这个目录里。
       
    clientPort： 这个端口就是客户端连接 Zookeeper 服务器的端口，Zookeeper 会监听这个端口，接受客户端的访问请求。
    
    至于最后的配置项：server.A = B:C:D: 
    A表示这个是第几号服务器,
    B 是这个服务器的 ip 地址；
    C 表示的是这个服务器与集群中的 Leader 服务器交换信息的端口；
    D 表示的是万一集群中的 Leader 服务器挂了，需要一个端口来重新进行选举，选出一个新的 Leader
# 🕹️Zookeeper开机自启动️
    vim /etc/init.d zookeeper
        #!/bin/bash
        #chkconfig:2345 20 90
        #description:zookeeper
        #processname:zookeeper
        export JAVA_HOME=/usr/java/jdk1.8.0_191
        export PATH=$PATH:$JAVA_HOME/bin:$PATH

        case $1 in
                start) su root /usr/local/zookeeper-3.4.5/bin/zkServer.sh start;;
                stop) su root /usr/local/zookeeper-3.4.5/bin/zkServer.sh stop;;
                status) su root /usr/local/zookeeper-3.4.5/bin/zkServer.sh status;;
                restart) su /usr/local/zookeeper-3.4.5/bin/zkServer.sh restart;;
                *) echo "require start|stop|status|restart" ;;
        esac
        
    分配权限: chmod 777 zookeeper
    开启启动配置: chkconfig zookeeper on
  