# 部署kubernetes集群(代理模式)
=====================================
安装前提，设置能够访问国外网站的代理。

关闭防火墙
-------------------------------------
<pre>
systemctl stop firewalld
systemctl disable firewalld
</pre>

关闭Selinux
---------------------------------------
<pre>
setenforce 0
sed -i --follow-symlinks 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
</pre>

修改内核参数
--------------------------------------
<pre>
modprobe br_netfilter
echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables
</pre>

关闭SWAP
---------------------------------------
<pre>
swapoff -a
sed -i 's/^\/dev\/mapper\/centos-swap/#&/' /etc/fstab
</pre>

安装docker-ce
---------------------------------------
<pre>
yum install -y yum-utils device-mapper-persistent-data lvm2
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum install -y docker-ce
</pre>

安装Kubernetes
<pre>
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg
        https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF
yum install -y kubelet kubeadm kubectl
</pre>
