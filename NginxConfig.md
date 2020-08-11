# 🌳Nginx1.8.10
## 安装简介
> 安装环境:CentOS 7
- 🚬下载
- 🥩环境准备
- 📦解压
- ⚒编译准备
- ⚙编译
- ⚗测试
# 🚬到Nginx官网下载Nginx1.8.10
   📞[nginx1.8.10](http://nginx.org/en/download.html)
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
        
# 🌳Nginx1.8.10核心配置(/usr/local/nginx/conf/nginx.conf)
         设置worker进程的用户，指的linux中的用户，会涉及到nginx操作目录或文件的一些权限，默认为nobody
         user root;
         worker进程工作数设置，一般来说CPU有几个，就设置几个，或者设置为N-1也行
         worker_processes 1;
         nginx 日志级别debug | info | notice | warn | error | crit | alert | emerg，错误级别从左到右越来越大  
         pid        logs/nginx.pid;(设置nginx进程 pid)
         设置工作模式
               events {
                   # 默认使用epoll
                   use epoll;
                   # 每个worker允许连接的客户端最大连接数
                   worker_connections  10240;
               }
         http 是指令块，针对http网络传输的一些指令配置
               http{
               }
         include 引入外部配置，提高可读性，避免单个配置文件过大
               include       mime.types;
         设定日志格式，main为定义的格式名称，如此 access_log 就可以直接使用这个变量了
               参数名	参数意义
               $remote_addr	客户端ip
               $remote_user	远程客户端用户名，一般为：’-’
               $time_local	时间和时区
               $request	请求的url以及method
               $status	响应状态码
               $body_bytes_send	响应客户端内容字节数
               $http_referer	记录用户从哪个链接跳转过来的
               $http_user_agent	用户所使用的代理，一般来时都是浏览器
               $http_x_forwarded_for	通过代理服务器来记录客户端的ip
               sendfile使用高效文件传输，提升传输性能。启用后才能使用tcp_nopush，是指当数据表累积一定大小后才发送，提高了效率。
         sendfile使用高效文件传输，提升传输性能。启用后才能使用tcp_nopush，是指当数据表累积一定大小后才发送，提高了效率。
               sendfile        on;
               tcp_nopush      on;
         keepalive_timeout设置客户端与服务端请求的超时时间，保证客户端多次请求的时候不会重复建立新的连接，节约资源损耗。
               #keepalive_timeout  0;
               keepalive_timeout  65;
         gzip启用压缩，html/js/css压缩后传输会更快
               gzip on;
         server可以在http指令块中设置多个虚拟主机
               listen 监听端口
               server_name localhost、ip、域名
               location 请求路由映射，匹配拦截
               root 请求位置
               index 首页设置
                   server {
                           listen       88;
                           server_name  localhost;
                   
                           location / {
                               root   html;
                               index  index.html index.htm;
                           }
                   }
           查看用户请求:
           cd /var/log/nginx
           vim access.log
#  📄nginx的常见错误:
              [error] open() "/var/run/nginx/nginx.pid" failed
              解决:mkdir /var/run/nginx
              [error] invalid PID number "" in "/var/run/nginx/nginx.pid"
              解决:./nginx -c /usr/local/nginx/conf/nginx.conf
#  ⚙nginx手动日志切割:
             创建cut_my_log.sh
               #!/bin/bash
               LOG_PATH="/var/log/nginx/"
               RECORD_TIME=$(date -d "yesterday" +%Y-%m-%d+%H:%M)
               PID=/var/run/nginx/nginx.pid
               mv ${LOG_PATH}/access.log ${LOG_PATH}/access.${RECORD_TIME}.log
               mv ${LOG_PATH}/error.log ${LOG_PATH}/error.${RECORD_TIME}.log
               #向Nginx主进程发送信号，用于重新打开日志文件
               kill -USR1 `cat $PID`
             添加可执行权限
               chmod +x cut_my_log.sh
             测试效果
               ./cut_my_log.sh
#  ⚙nginx日志切割-定时:
           安装定时任务:
               yum install crontabs
           crontab -e 编辑并且添加一行新的任务:
               */1 * * * * /usr/local/nginx/sbin/cut_my_log.sh
           重启定时任务:service crond restart
           常用定时任务列表:
               service crond start         //启动服务
               service crond stop          //关闭服务
               service crond restart       //重启服务
               service crond reload        //重新载入配置
               crontab -e                  // 编辑任务
               crontab -l                  // 查看任务列表
           定时任务表达式：
           Cron表达式是，分为5或6个域，每个域代表一个含义，如下所示：
           分	时	日	月	星期几	年（可选）
           取值范围	0-59	0-23	1-31	1-12	1-7	2019/2020/2021/…
           常用表达式:
           每分钟执行:
           */1 * * * *
           每日凌晨（每天晚上23:59）执行
           59 23 * * *
           每日凌晨1点执行:
           0 1 * * *
# ⚙nginx gzip
               开启:gzip on;
               限制最小压缩:gzip_min_length 1;
               压缩级别:gzip_comp_level 3;
               压缩文件类型:
                   gzip_typs text/plain application/javascript application/x-javascript text/css
                   application/xml text/javascript application/x-httpd-php image/jpeg image/git image/png application/json
# ⚙nginx root 和 alias
           假如服务器路径为：/home/imooc/files/img/face.png
           root 路径完全匹配访问
           配置的时候为:
           location /imooc {
               root /home
           }
           用户访问的时候请求为：url:port/imooc/files/img/face.png
           alias 可以为你的路径做一个别名，对用户透明
           配置的时候为:
           location /hello {
               alias /home/imooc
           }
           用户访问的时候请求为：url:port/hello/files/img/face.png，如此相当于为目录imooc做一个自定义的别名。
# ⚙nginx location匹配规则
          location 的匹配规则
          空格：默认匹配，普通匹配
          location / {
               root /home;
          }
          
          =：精确匹配
          location = /imooc/img/face1.png {
              root /home;
          }
          
          ~*：匹配正则表达式，不区分大小写
          #符合图片的显示
          location ~* .(GIF|jpg|png|jpeg) {
              root /home;
          }
          
          ~：匹配正则表达式，区分大小写
          #GIF必须大写才能匹配到
          location ~ .(GIF|jpg|png|jpeg) {
              root /home;
          }
          
          ^~：以某个字符路径开头
          location ^~ /imooc/img {
              root /home;
          }
# ⚙nginx跨域配置
           #允许跨域请求的域，*代表所有
           add_header 'Access-Control-Allow-Origin' *;
           #允许带上cookie请求
           add_header 'Access-Control-Allow-Credentials' 'true';
           #允许请求的方法，比如 GET/POST/PUT/DELETE
           add_header 'Access-Control-Allow-Methods' *;
           #允许请求的header
           add_header 'Access-Control-Allow-Headers' *;
# ⚙nginx防盗链
           #对源站点验证
           valid_referers *.imooc.com; 
           #非法引入会进入下方判断
           if ($invalid_referer) {
               return 404;
           }
# ⚙nginx负载均衡
              配置上有服务器
               upstream tomcats{
                    server 192.168.182.151:8080;
                    server 192.168.182.152:8080;
                    server 192.168.182.153:8080;
               }
               配置反向代理监听端口
               server {
                       listen       80;
                       server_name  shop.z.mukewang.com;
             
                       location / {
                           proxy_pass http://tomcats;
                       }
               }
# ⚙nginx指令参数max_conns
          max_conns:限制每台最大连接数
          worker进程设置1个，便于测试观察成功的连接数
          worker_processes  1;
          
          upstream tomcats {
                  server 192.168.1.173:8080 max_conns=2;
                  server 192.168.1.174:8080 max_conns=2;
                  server 192.168.1.175:8080 max_conns=2；
          }
 # ⚙nginx指令参数weight=1
           weight:每台服务器分配到的权重
         
           upstream tomcats {
                   server 192.168.1.173:8080 weight=1;
                   server 192.168.1.174:8080 weight=2;
                   server 192.168.1.175:8080 weight=3；
           }
 # ⚙nginx指令参数slow_start
           商业版
               upstream tomcats {
                       server 192.168.1.173:8080 weight=6 slow_start=60s;
               #       server 192.168.1.190:8080;
                       server 192.168.1.174:8080 weight=2;
                       server 192.168.1.175:8080 weight=2;
               }
           注意:
               该参数不能使用在hash和random load balancing中
               如果在upstream中只有一台server，则该参数失败
 # ⚙nginx指令参数down
            指定某台服务器节点不可用
               upstream tomcats {
                       server 192.168.1.173:8080 down;
               #       server 192.168.1.190:8080;
                       server 192.168.1.174:8080 weight=1;
                       server 192.168.1.175:8080 weight=1;
               }
 # ⚙nginx指令参数backup
            当服务器节点中的所有机器宕机，启动被backup标记的备用机
                  upstream tomcats {
                          server 192.168.1.173:8080 backup;
                  #       server 192.168.1.190:8080;
                          server 192.168.1.174:8080 weight=1;
                          server 192.168.1.175:8080 weight=1;
                  }
            注意:
                该参数不能使用在hash和random load balancing中
# ⚙upstream 指令参数 max_fails、fail_timeout
            max_fails：表示失败几次，则标记server已宕机，剔出上游服务。
            fail_timeout：表示失败的重试时间。
            假设目前设置如下:
              max_fails=2 fail_timeout=15s 
              则代表在15秒内请求某一server失败达到2次后，
              则认为该server已经挂了或者宕机了，随后再过15秒，
              这15秒内不会有新的请求到达刚刚挂掉的节点上，
              而是会请求到正常运作的server，
              15秒后会再有新请求尝试连接挂掉的server，如果还是失败，重复上一过程，直到恢复。
# ⚙nginx keepalive
            keepalive:设置长连接处理数量(提供吞吐量)
            proxy_http_version 1.1; 设置长连接版本为1.1
            proxy_set_header Connection ""; 清除connction header信息
# ⚙nginx ip_hash
            ip_hash 通过用户访问的本地ip来做负载均衡
            upstream tomcats {
                    ip_hash;
                    
                    server 192.168.1.173:8080;
                    server 192.168.1.174:8080 down;
                    server 192.168.1.175:8080;
            }
# ⚙nginx url_hash 与 least_conn
            url_hash 根据每次请求url地址，hash后访问到固定的服务器节点
            least_conn 最少连接数
            upstream tomcats {
                # url hash
                hash $request_uri;
                # 最少连接数
                # least_conn
            
                server 192.168.1.173:8080;
                server 192.168.1.174:8080;
                server 192.168.1.175:8080;
            }
            
            server {
                listen 80;
                server_name www.tomcats.com;
            
                location / {
                    proxy_pass  http://tomcats;
                }
            }  
 # ⚙nginx缓存
               浏览器缓存：            
               加速用户访问，提升单个用户（浏览器访问者）体验，缓存在本地
               Nginx缓存
               缓存在nginx端，提升所有访问到nginx这一端的用户
               提升访问上游（upstream）服务器的速度
               用户访问仍然会产生请求流量
               控制浏览器缓存：
               location /files {
                   alias /home/imooc;
                   # expires 10s;
                   # expires @22h30m;
                   # expires -1h;
                   # expires epoch;
                   # expires off;
                   expires max;
               }
 # ⚙nginx反向代理缓存
               # proxy_cache_path 设置缓存目录
               #       keys_zone 设置共享内存以及占用空间大小
               #       max_size 设置缓存大小
               #       inactive 超过此时间则被清理
               #       use_temp_path 临时目录，使用后会影响nginx性能
               proxy_cache_path /usr/local/nginx/upstream_cache keys_zone=mycache:5m max_size=1g inactive=1m use_temp_path=off;
               
               location / {
                   proxy_pass  http://tomcats;
               
                   # 启用缓存，和keys_zone一致
                   proxy_cache mycache;
                   # 针对200和304状态码缓存时间为8小时
                   proxy_cache_valid   200 304 8h;
               }
 # ⚙nginx配置安全域名连接
              1. 安装SSL模块
              要在nginx中配置https，就必须安装ssl模块，也就是: http_ssl_module。
              
              进入到nginx的解压目录： /home/software/nginx-1.16.1
              
              新增ssl模块(原来的那些模块需要保留)
              
              ./configure \
              --prefix=/usr/local/nginx \
              --pid-path=/var/run/nginx/nginx.pid \
              --lock-path=/var/lock/nginx.lock \
              --error-log-path=/var/log/nginx/error.log \
              --http-log-path=/var/log/nginx/access.log \
              --with-http_gzip_static_module \
              --http-client-body-temp-path=/var/temp/nginx/client \
              --http-proxy-temp-path=/var/temp/nginx/proxy \
              --http-fastcgi-temp-path=/var/temp/nginx/fastcgi \
              --http-uwsgi-temp-path=/var/temp/nginx/uwsgi \
              --http-scgi-temp-path=/var/temp/nginx/scgi  \
              --with-http_ssl_module
              编译和安装
              
              make
              
              make install
              2. 配置HTTPS
              把ssl证书 *.crt 和 私钥 *.key 拷贝到/usr/local/nginx/conf目录中。
              
              新增 server 监听 443 端口：
              
              server {
                  listen       443;
                  server_name  www.imoocdsp.com;
              
                  # 开启ssl
                  ssl     on;
                  # 配置ssl证书
                  ssl_certificate      1_www.imoocdsp.com_bundle.crt;
                  # 配置证书秘钥
                  ssl_certificate_key  2_www.imoocdsp.com.key;
              
                  # ssl会话cache
                  ssl_session_cache    shared:SSL:1m;
                  # ssl会话超时时间
                  ssl_session_timeout  5m;
              
                  # 配置加密套件，写法遵循 openssl 标准
                  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
                  ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
                  ssl_prefer_server_ciphers on;
                  
                  location / {
                      proxy_pass http://tomcats/;
                      index  index.html index.htm;
                  }
               }
              3. reload nginx
              ./nginx -s reload   
# ⚙nginx部署配置(nginx.conf)
            
               user root;
               worker_processes 2;
               
               #error_log  logs/error.log;
               #error_log  logs/error.log  notice;
               #error_log  logs/error.log  info;
               
               #pid        logs/nginx.pid;
               
               
               events {
                   #use epoll liunx默认使用
                   #每个worker进程客户端最大连接数
                   worker_connections  10240;
               }
               
               
               http {
                   include       mime.types;
                   default_type  application/octet-stream;
               
               
               
                   sendfile        on;
               
                   keepalive_timeout  65;
               
                  upstream api.z.mukewang.com{
                   #server 192.168.182.151:8080;
                   #server 192.168.182.152:8080;
                   #server 192.168.182.153:8080;	
                   server 192.168.182.150:8088;	
                  }
                   
                 
                  server{
                   listen 80;
                   server_name api.z.mukewang.com;
                   
                   location / {
                           proxy_pass http://api.z.mukewang.com;
                   }
               
                  }
                   server {
                       listen       80;
                       server_name  shop.z.mukewang.com;
               
                       add_header 'Access-Control-Allow-Origin' *;
                       add_header 'Access-Control-Allow-Credentials' 'true';
                       add_header 'Access-Control-Allow-Methods' *;
                       add_header 'Access-Control-Allow-Headers' *;
               
                       location / {
                           root /home/webapps/foodie-shop;
                           index index.html;
                       }
                   
                       error_page   500 502 503 504  /50x.html;
                       location = /50x.html {
                           root   html;
                       }
               
                   }
                   server {
                       listen       80;
                       server_name  center.z.mukewang.com;
               
                       add_header 'Access-Control-Allow-Origin' *;
                       add_header 'Access-Control-Allow-Credentials' 'true';
                       add_header 'Access-Control-Allow-Methods' *;
                       add_header 'Access-Control-Allow-Headers' *;
               
                       location / {
                           root /home/webapps/foodie-center;
                           index index.html;
                       }
               
                       error_page   500 502 503 504  /50x.html;
                       location = /50x.html {
                           root   html;
                       }
               
                   }
               
               }