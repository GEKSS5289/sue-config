# centos安装docker
## yum install -y yum-utils device-mapper-persistent-data lvm2
## yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
## yum list docker-ce --showduplicates | sort -r
## sudo yum install docker-ce-18.06.1.ce  
## systemctl enable docker
## systemctl start docker
## systemctl status docker
## docker version
