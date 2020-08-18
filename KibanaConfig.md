# 📃Kibana6.6.0 
## 安装简介
   - 🚬下载Kibana6.6.0
   - 📦解压
   - 🛠配置
   - 🍻启动
# 🚬下载FileBeat6.6.0
  ### 🤝作者提供:[Kibana](https://shushun.oss-cn-shenzhen.aliyuncs.com/software/kibana-6.6.0-linux-x86_64.tar.gz)  
# 📦解压
        tar -zxvf kibana-6.6.0-linux-x86_64.tar.gz -C /usr/local/
        cd /usr/local
        mv kibana-6.6.0-linux-x86_64/ kibana-6.6.0
# 🛠配置
 
   ##### ✏修改Kibana.yml (将抓取到的信息发送到kafka)
             vim /usr/local/kibana-6.6.0/config/kibana.yml
                   server.host: "0.0.0.0"
                   server.name: "192.168.11.35"
                   elasticsearch.hosts: ["http://192.168.11.35:9200"]
                   elasticsearch.username: "elastic"
                   elasticsearch.password: "123456"
# 🍻启动
        
        /usr/local/kibana-6.6.0/bin/kibana -c /usr/local/kibana-6.6.0/config/kibana.yml
      
        http://192.168.0.236:5601
# 🌈成功
  ![avatar](https://shushun.oss-cn-shenzhen.aliyuncs.com/success-image/QQ%E6%88%AA%E5%9B%BE20200818160826.png)