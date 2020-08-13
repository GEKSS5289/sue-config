# 🦻Logstatsh6.4.3
## 安装简介
> 安装环境:CentOS 7
- 🚬下载
- 📦解压
- ⚗测试
# 🚬下载Logstatsh6.4.3
 ###  📞官方提供:[Logstatsh6.4.3](https://www.elastic.co/cn/downloads/past-releases/logstash-6-4-3)
# 📦解压   
    tar -zxvf /home/software/logstash-6.4.3.tar.gz -C /usr/local/
# ⚗测试
    cd /logstash-6.4.3/bin
    ./logstatsh -f /usr/local/logstash-6.4.3/sync/logstatsh-db-sync.conf 
> (注:Sync是在logstatsh-6.4.3文件夹下自己创建的文件夹) 详见[PlayLogstatsh](https://github.com/GEKSS5289/sue-play/blob/master/PlayLogstatsh.md)
  (注:logstatsh-db-sync.conf是自己创建的配置文件 内容需要自己查阅书写)详见[PlayLogstatsh](https://github.com/GEKSS5289/sue-play/blob/master/PlayLogstatsh.md)
# 🌈成功
    没有error信息，看到预执行的查询则为成功
        
