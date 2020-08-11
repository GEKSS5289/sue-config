# 🌳Nginx1.8.10
## 安装简介
> 安装环境:CentOS 7
- 🚬下载
- 🥩环境准备
- 📦解压
- ⚒编译准备
- ⚙编译
- ⚗测试
# 🚬下载Nginx1.8.10
 ###  📞官方提供:[nginx1.8.10](http://nginx.org/en/download.html)
 ###  🤝作者提供:[nginx1.8.10](https://shushun.oss-cn-shenzhen.aliyuncs.com/software/nginx-1.18.0.tar.gz)
# 🥩环境准备   
    💿安装gcc环境:  yum install gcc-c++
    💿安装PCRE库,用于解析正则表达式:yum install -y pcre pcre-devel
    💿安装zlib库和解压缩依赖:yum install -y zlib zlib-devel
    💿安装SSL安全加密套接字的协议层，用于http安全传输，也就是https: yum install -y openssl openssl-devel
# 📦解压
    tar -zxvf nginx-1.8.10.tar.gz -C /usr/local/
# ⚒编译准备
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
                           --http-scgi-temp-path=/var/temp/nginx/scgizoo_sample.cfg
                           --with-http_ssl_module
    😊命令解释:
           –prefix	指定nginx安装目录
           –pid-path	指向nginx的pid
           –lock-path	锁定安装文件，防止被恶意篡改或误操作
           –error-log	错误日志
           –http-log-path	http日志
           –with-http_gzip_static_module	启用gzip模块，在线实时压缩输出数据流
           –http-client-body-temp-path	设定客户端请求的临时目录
           –http-proxy-temp-path	设定http代理临时目录
           –http-fastcgi-temp-path	设定fastcgi临时目录
           –http-uwsgi-temp-path	设定uwsgi临时目录
           –http-scgi-temp-path	设定scgi临时目录
# ⚙编译
    make
    make insatll
# ⚗测试
    cd /usr/local/nginx/sbin
    启动./nginx 
# 🌈成功
   ![avatar](https://shushun.oss-cn-shenzhen.aliyuncs.com/QQ%E6%88%AA%E5%9B%BE20200811122920.png)
        
