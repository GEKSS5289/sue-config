# 🐬Mysql8.0
> ### 💿安装环境CentOS 7
> ### 🐬安装Mysql8.0
## 🐬安装Mysql8.0
    检查:yum -y remove MySQL-*
        如果系统中存在:find / -name mysql
        删除配置文件:rm -rf /etc/my.cnf
        删除mysql默认密码:rm -rf /root/.mysql_sercret
        安装源:rpm -Uvh https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm
        安装mysql8.0:yum --enablerepo=mysql80-community install mysql-community-server
        启动服务:service mysqld start
        查看状态:service mysqld status
        查看临时密码:grep "A temporary password" /var/log/mysqld.log
        更改临时密码:ALTER USER 'root'@'localhost' IDENTIFIED mysql_native_password BY 'new password';
        修改root用户的host为远程可访问: 
                    use mysql
                    update user set host='%' where user = 'root';
        配置远程访问:GRANT ALL ON *.* TO 'root'@'%';
        
        其他命令:
            重置密码: 
                在/etc/my.cnf 中[mysqld]后
                添加skip-grant-tables 重启服务 输入mysql
                ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'ShuShun@1558';(如果遇到问题则flush privileges;在重试此命令)