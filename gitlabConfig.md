# GitLab yum安装

## 1.yum install https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/yum/el7/gitlab-ce-13.2.3-ce.0.el7.x86_64.rpm 
## 2.gitlab-ctl reconfigure

# docker install gitlab
> docker run --detach \
  --hostname gitlab.example.com \
  --publish 443:443 --publish 80:80 --publish 225:22 \
  --name gitlab \
  --restart always \
  --volume $GITLAB_HOME/config:/etc/gitlab \
  --volume $GITLAB_HOME/logs:/var/log/gitlab \
  --volume $GITLAB_HOME/data:/var/opt/gitlab \
  gitlab/gitlab-ce:latest
