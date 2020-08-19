# 📡SkyWalking6.6.0
## 安装简介
> 安装环境:CentOS 7 JDK1.8
- 🚬下载
- 📦解压
- 🛠配置
- ⚗测试
# 🚬下载JDK1.8
 ###  📞官方提供:[SkyWalking6.6.0](https://archive.apache.org/dist/skywalking/6.6.0/)
 ###  🤝作者提供:[SkyWalking6.6.0](https://shushun.oss-cn-shenzhen.aliyuncs.com/software/jdk-8u191-linux-x64.tar.gz)
# 📦解压   
    tar -zxvf /home/software/jdk-8u191-linux-x64.tar.gz -C /usr/local/java/
# 🛠配置环境变量
    ✏vim /etc/profile/ (在unset -f pathmunge下方空白处添加以下配置)
        export JAVA_HOME=/usr/java/jdk1.8.0_191
        export CLASSPATH=.:%JAVA_HOME%/lib/dt.jar:%JAVA_HOME%/lib/tools.jar  
        export PATH=$PATH:$JAVA_HOME/bin
    ☕刷新配置文件:
        source /etc/profile
# ⚗测试
    java -version
# 🌈成功
    java version "1.8.0_191"
    Java(TM) SE Runtime Environment (build 1.8.0_191-b12)
    Java HotSpot(TM) 64-Bit Server VM (build 25.191-b12, mixed mode)
        
