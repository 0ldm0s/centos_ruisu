注意：仅限centos 7使用 

注意：下面命令没有特别说明的话，默认为**root**账户执行 

```sh
cd
git clone https://github.com/0ldm0s/centos_ruisu.git
```

强制内核降级

```sh
cd centos_ruisu
rpm -ivh kernel-3.10.0-123.el7.x86_64.rpm --force
rpm -ivh kernel-headers-3.10.0-123.el7.x86_64.rpm --force
rpm -ivh kernel-devel-3.10.0-123.el7.x86_64.rpm --force
# 检查是否安装成功
egrep ^menuentry /etc/grub2.cfg | cut -f 2 -d \'
# 一般默认为0，但不排除特例。确认3.10.0-123的启动顺位，设置引导的内核
grub2-set-default 0
reboot

cd centos_ruisu
bash ./appex.sh 'install'
```



可选优化

```sh
cat >> /etc/security/limits.conf <<-EOF
* soft nofile 51200
* hard nofile 51200
EOF
# 最好先检查一下是否有重复
cat >> /etc/sysctl.conf <<-EOF
fs.file-max = 51200
net.core.rmem_max = 67108864
net.core.wmem_max = 67108864
net.core.netdev_max_backlog = 250000
net.core.somaxconn = 4096
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_tw_recycle = 0
net.ipv4.tcp_fin_timeout = 30
net.ipv4.tcp_keepalive_time = 1200
net.ipv4.ip_local_port_range = 10000 65000
net.ipv4.tcp_max_syn_backlog = 8192
net.ipv4.tcp_max_tw_buckets = 5000
net.ipv4.tcp_fastopen = 3
net.ipv4.tcp_mem = 25600 51200 102400
net.ipv4.tcp_rmem = 4096 87380 67108864
net.ipv4.tcp_wmem = 4096 65536 67108864
net.ipv4.tcp_mtu_probing = 1
net.core.default_qdisc=fq
EOF
sysctl -p
reboot
```

