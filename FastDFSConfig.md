# ☕FastDFS
## 安装简介
> 安装环境:CentOS 7(两台计算机节点 一台安装tracker 一台安装storage)
- 🚬下载
- 🥩环境准备
- 📦解压与安装(libfastcommon-1.0.42.tar.gz)
- 📦解压与安装(fastdfs-6.04.tar.gz)
- 🥪拷贝配置文件到/etc/fdfs
- 🛠配置tracker服务(第一台计算机节点)
- ⚗测试tracker服务
- 🛠配置storage服务(第二胎计算机节点)
- ⚗测试storage服务
- ⚗测试上传
- 🛠配置nginx fastdfs实现文件服务器
- 💿安装nginx
- 🛠其他配置
# 🚬下载FastDFS相关包
 ###  📞官方提供:[FastDFS](https://github.com/happyfish100)
 ###  🤝作者提供:[FastDFS](https://shushun.oss-cn-shenzhen.aliyuncs.com/software/FastDFS.zip)
 > FastDFS压缩包中有四个压缩包后续依次解压
# 🥩环境准备
    yum install -y gcc gcc-c++
    yum -y install libevent
# 📦🔩解压与安装(libfastcommon-1.0.42.tar.gz)   
    #安装libfastcommon-1.0.42函数库
    tar -zxvf libfastcommon-1.0.42.tar.gz
    #进入libfastcommon文件夹，编译并且安装
    ./make.sh
    ./make.sh install
# 📦🔩解压与安装(fastdfs-6.04.tar.gz)   
    #安装fastdfs主程序
    tar -zxvf fastdfs-6.04.tar.gz
    #进入fastdfs-6.04文件夹，编译并且安装
    ./make.sh
    ./make.sh install
# 🥪拷贝配置文件到/etc/fdfs
    cp /home/software/FastDFS/fastdfs-6.04/conf/* /etc/fdfs/
# 🛠配置tracker服务(第一台计算机节点)
    ✏修改tracker.conf配置文件，此为tracker的工作目录，保存数据以及日志修改
        vim /etc/fdfs/tracker.conf
            base_path=/usr/local/fastdfs/tracker (需要自己先创建:mkdir /usr/local/fastdfs/tracker -p)
# ⚗测试tracker服务
    /usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf
    ps -ef|grep tracker (查看是否存在tracker进程 存在即成功)
    停止:/usr/bin/stop.sh /etc/fdfs/tracker.conf
# 🛠配置storage服务(第二胎计算机节点)
    ✏修改storage.conf配置文件
        vim /etc/fdfs/storage.conf
            # 修改组名
            group_name=imooc
            # 修改storage的工作空间
            base_path=/usr/local/fastdfs/storage (需要自己先创建:mkdir /usr/local/fastdfs/storage -p)
            # 修改storage的存储空间
            store_path0=/usr/local/fastdfs/storage
            # 修改tracker的地址和端口号，用于心跳
            tracker_server=192.168.1.153:22122
            # 后续结合nginx的一个对外服务端口号
            http.server_port=8888
# ⚗测试storage服务
    /usr/bin/fdfs_storaged /etc/fdfs/storage.conf
    ps -ef|grep storage (查看是否存在storage进程 存在即成功)
   
# ⚗测试上传
    ✏修改client.conf配置文件(第二台计算机节点)
          base_path=/usr/local/fastdfs/client (需要自己先创建:mkdir /usr/local/fastdfs/client -p)
          tracker_server=192.168.1.153:22122 (tracker服务地址加端口号)
    上传文件:/usr/bin/fdfs_test /etc/fdfs/client.conf upload /home/logo.png
# 🛠配置nginx fastdfs实现文件服务器   
    cd /home/software/FastDFS
    解压nginx插件
    tar -zxvf fastdfs-nginx-module-1.22.tar.gz 
    复制配置文件    
    cd /fastdfs-nginx-module-1.22/src
    cp mod_fastdfs.conf /etc/fdfs
    修改/fastdfs-nginx-module/src/config文件
        将ngx_module_incs="/usr/local/include" 改成 ngx_module_incs="/usr/include"
        将CORE_INCS="$CORE_INCS /usr/local/include" 改成 CORE_INCS="$CORE_INCS /usr/include"
# 💿安装nginx
    📁首先创建nginx临时目录:mkdir /var/temp/nginx -p
    ⚙在nginx目录下编译(直接在命令行输入如下指得到makefile):
                           ./configure \
                           --prefix=/usr/local/nginx \
                           --conf-path=/usr/local/nginx/conf/nginx.conf \
                           --pid-path=/var/run/nginx/nginx.pid \
                           --lock-path=/var/lock/nginx.lock \
                           --error-log-path=/var/log/nginx/error.log \
                           --http-log-path=/var/log/nginx/access.log \
                           --with-http_gzip_static_module \
                           --http-client-body-temp-path=/var/temp/nginx/client \
                           --http-proxy-temp-path=/var/temp/nginx/proxy \
                           --http-fastcgi-temp-path=/var/temp/nginx/fastcgi \
                           --http-uwsgi-temp-path=/var/temp/nginx/uwsgi \
                           --http-scgi-temp-path=/var/temp/nginx/scgizoo_sample.cfg \
                           --with-http_ssl_module \
                           --add-module=/home/software/fdfs/fastdfs-nginx-module-1.22/src
# 🛠其他配置
    修改mod_fastdfs.conf配置文件
    vim /etc/fdfs/mod_fastdfs.conf  
      base_path=/usr/local/fastdfs/tmp (需要自己创建mkdir /usr/local/fastdfs/tmp)
      tracker_server=192.168.1.153:22122 (配置tracker服务地址)
      group_name=sue
      url_have_group_name = true (包含组名)
      store_path0=/usr/local/fastdfs/storage  
    修改nginx配置文件(nginx.conf)
      server {
          listen       8888;
          server_name  localhost;
      
          location /imooc/M00 {
                  ngx_fastdfs_module;
          }
      
      }