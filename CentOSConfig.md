# 🎮CentOS 7
## 配置简介
- 📟虚拟机固定IP设置
- 🔒虚拟机开放端口
# 📟虚拟机固定IP设置
    vim /etc/sysconfig/network-scripts/ifcfg-ens33
    📁添加:
        BOOTPROTO=static
        IPADDR=192.168.182.152
        DNS1=192.168.2.1（填写自己的DNS解析）
        GATEWAY=192.168.2.1(填写自己的默认网关)
# 🔓虚拟机开放端口
    查看放火墙状态:systemctl status firewalld
    关闭放火墙:systemctl stop firewalld
    开启放火墙:systemctl start firewalld 
    开放端口号:firewall-cmd --zone=public --add-port=需要开放的端口号/tcp --permanent
    关闭端口号:firewall-cmd --zone=public --remove-port=需要关闭的端口号/tcp --permanent
    查看所有开放端口号:firewall-cmd --zone=public --list-ports
    重新加载配置文件:firewall-cmd --reload