# 部署kubernetes集群(代理模式)
=====================================

关闭防火墙
-------------------------------------
  systemctl stop firewalld<br/>
  systemctl disable firewalld<br/>

关闭Selinux
---------------------------------------
setenforce 0<br/>
sed -i --follow-symlinks 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux<br/>

修改内核参数
--------------------------------------
modprobe br_netfilter<br/>
echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables<br/>

关闭SWAP
---------------------------------------
swapoff -a<br/>
sed -i 's/^\/dev\/mapper\/centos-swap/#&/' /etc/fstab
