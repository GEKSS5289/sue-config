# ☕FastDFS
## 安装简介
> 安装环境:CentOS 7(两台计算机节点 一台安装tracker 一台安装storage)
- 🚬下载
- 🥩环境准备
- 📦解压与安装(libfastcommon-1.0.42.tar.gz)
- 📦解压与安装(fastdfs-6.04.tar.gz)
- 🥪拷贝配置文件到/etc/fdfs
- 🛠配置tracker服务
- ⚗测试
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
# 🛠配置tracker服务
    ✏修改tracker配置文件，此为tracker的工作目录，保存数据以及日志修改
        base_path=/usr/local/fastdfs/tracker (需要自己先创建:mkdir /usr/local/fastdfs/tracker -p)
# ⚗测试
   /usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf
   ps -ef|grep tracker (查看是否存在tracker进程 存在即成功)
   停止:/usr/bin/stop.sh /etc/fdfs/tracker.conf
        
