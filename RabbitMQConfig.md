# 🐰RabbitMQ 3.6.5
## 安装简介
> 安装环境:CentOS 7
- 🚬下载相关组件
- 🎬环境准备
- 💿安装组件
- ⚙配置
- 💿安装管理插件
- ⚗测试
# 🚬下载🐰RabbitMQ 3.6.5
 ###  🤝作者提供:[RabbitMQ 3.6.5](https://www.shushunstudio.com/software/RabbitMQ3.6.5.zip)
# 🎬环境准备
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