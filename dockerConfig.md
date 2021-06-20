# centos安装docker

## 创建docker-install.sh
`vim docker-install.sh`
## 写入docker-install脚本
```
yum install -y yum-utils device-mapper-persistent-data lvm2
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum list docker-ce --showduplicates | sort -r
sudo yum install docker-ce-18.06.1.ce  
systemctl enable docker
systemctl start docker
systemctl status docker
docker version
```

# docker-componse安装
>curl -L https://github.com/docker/compose/releases/download/1.26.2/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose


>chmod +x /usr/local/bin/docker-compose


>ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
