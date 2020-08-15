# 🐰RabbitMQ 3.6.5
## 🧭安装简介
> ### 安装环境:CentOS 7
> ##### 🚬下载相关组件
> ##### 🎬环境准备
> ##### 💿安装组件
> ##### ⚙配置
> ##### 💿安装管理插件
> ##### ⚗测试
# 🚬下载🐰RabbitMQ 3.6.5
 ###  🤝作者提供:[RabbitMQ 3.6.5](https://www.shushunstudio.com/software/RabbitMQ3.6.5.zip)
# 🎬环境准备
    修改三台计算机节点的 /etc/hosts和/etc/hostname
    vim /etc/hosts (节点1为例)                   
        192.168.182.150 sue1 (节点1自己)        
        192.168.182.151 sue2 (节点2)          | 节点1 192.168.182.150
        192.168.182.152 sue3 (节点3)
    vim /etc/hostname(节点1为例)
        sue1
    ----------------------------------------    
    vim /etc/hosts (节点2为例)
            192.168.182.150 sue1 (节点1)
            192.168.182.151 sue2 (节点2自己)  | 节点2 192.168.182.151
            192.168.182.152 sue3 (节点3)
    vim /etc/hostname(节点2为例)
        sue2
     ----------------------------------------      
    vim /etc/hosts (节点3为例)
            192.168.182.150 sue1 (节点1)
            192.168.182.151 sue2 (节点2)      | 节点3 192.168.182.152
            192.168.182.152 sue3 (节点3自己)
    vim /etc/hostname(节点3为例)
        sue3
        
    下载基础软件包  
    yum install build-essential openssl openssl-devel unixODBC unixODBC-devel make gcc gcc-c++ kernel-devel m4 ncurses-devel tk tc xz
# 💿安装组件
    rpm -ivh erlang-18.3-1.el7.centos.x86_64.rpm 
    rpm -ivh socat-1.7.3.2-1.1.el7.x86_64.rpm --force --nodeps
    rpm -ivh rabbitmq-server-3.6.5-1.noarch.rpm
# ⚙配置
    修改用户登录与连接心跳检测，注意修改
    vim /usr/lib/rabbitmq/lib/rabbitmq_server-3.6.5/ebin/rabbit.app
    修改点1：loopback_users 中的 <<"guest">>,只保留guest （用于用户登录）
    修改点2：heartbeat 为10（用于心跳连接）
    
# 💿安装管理插件
    首先启动
    /etc/init.d/rabbitmq-server start
    启用管理插件
    rabbitmq-plugins enable rabbitmq_management
    查看是否启动
    lsof -i:15672 或者 netstat -tnlp | grep 15672
# ⚗测试
    http://ip:15672
# 🌈成功
   ![avatar](http://www.shushunstudio.com/success-image/QQ%E6%88%AA%E5%9B%BE20200814182108.png)

# 🌾RabbitMQ镜像集群搭建
  ### 💡文件同步
        PS:选择150、151、152任意一个节点为Master（这里选择150为Master），
        也就是说我们需要把150的Cookie文件同步到151、152节点上去，
        进入/var/lib/rabbitmq目录下，

        //进入目录修改权限；远程copy77、78节点
        scp /var/lib/rabbitmq/.erlang.cookie 192.168.11.77:/var/lib/rabbitmq/
        scp /var/lib/rabbitmq/.erlang.cookie 192.168.11.78:/var/lib/rabbitmq/
  ### 💡停止MQ服务
        停止三个节点MQ服务
        rabbitmqctl stop
  ### 💡组成集群操作
        PS:接下来我们就可以使用集群命令，配置76、77、78为集群模式，3个节点（76、77、78）执行启动命令，后续启动集群使用此命令即可。
        
        rabbitmq-server -detached
  ### 💡slave加入集群操作（重新加入集群也是如此，以最开始的主节点为加入节点）
        从节点1：rabbitmqctl stop_app
        从节点1：rabbitmqctl join_cluster --ram rabbit@主节点(76)
        从节点1：rabbitmqctl start_app
        从节点2：rabbitmqctl stop_app
        从节点2：rabbitmqctl join_cluster rabbit@主节点(76)
        从节点2：rabbitmqctl start_app
        
        另外:其他节点上操作要移除的集群节点
        rabbitmqctl forget_cluster_node rabbit@(移除的节点hostname)
  ### 💡修改集群名称(任意节点都可以修改)
        rabbitmqctl set_cluster_name rabbitmq_cluster1
  ### 💡查看集群状态(任意节点都可以查看)
        rabbitmqctl cluster_status
  ### 🍹配置镜像队列(任意一个节点上执行)
        rabbitmqctl set_policy ha-all "^" '{"ha-mode":"all"}'
  # 🗿成功
   ## 进入控制台能看到如下:
   ### ![avatar](http://www.shushunstudio.com/success-image/QQ%E6%88%AA%E5%9B%BE20200815124516.png)
   ## 创建新队列测试会看到如下:
   ### ![avatar](http://www.shushunstudio.com/success-image/QQ%E5%9B%BE%E7%89%8720200815124500.png)