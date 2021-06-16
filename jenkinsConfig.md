# docker-install-jenkins
> 安装jdk8

>安装maven

docker run -d --name jenkins -p 8080:8080 -p 50000:50000 -u root \
 -v /opt/jenkins_home:/var/jenkins_home \
 -v /var/run/docker.sock:/var/run/docker.sock \
 -v /usr/bin/docker:/usr/bin/docker \ 
 -v /usr/local/maven:/usr/local/maven \
 -v /usr/local/jdk:/usr/local/jdk \ 
 -v /etc/localtime:/etc/localtime \
 --restart = always \
 --name jenkins jenkins/jenkins:lts
