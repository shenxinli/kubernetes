# 部署kubernetes集群(代理模式)
安装前提，设置能够访问国外网站的代理。

## 安装环境
<pre>
192.168.4.129      k8s-master
192.168.4.130      k8s-node1
</pre>

## 安装kubernetes

关闭防火墙
<pre>
systemctl stop firewalld
systemctl disable firewalld
</pre>

关闭Selinux
<pre>
setenforce 0
sed -i --follow-symlinks 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
</pre>

修改内核参数
<pre>
modprobe br_netfilter
echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables
</pre>

关闭SWAP
<pre>
swapoff -a
sed -i 's/^\/dev\/mapper\/centos-swap/#&/' /etc/fstab
</pre>

安装docker-ce
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

## Kubernetes集群初始化
进入k8s-master主机
<pre>
kubeadm init --apiserver-advertise-address=192.168.4.129 --pod-network-cidr=192.168.0.0/16
</pre>

启动后，根据提示运行
<pre>
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
</pre>

安装flannel网络环境
<pre>
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
</pre>

以上步骤，若没有错误，表示初始化成功。

验证集群初始化
<pre>
kubectl get nodes
kubectl get pods --all-namespaces
</pre>
