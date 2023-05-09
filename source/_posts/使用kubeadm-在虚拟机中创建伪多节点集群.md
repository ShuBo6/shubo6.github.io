---
title: 使用containerd+kubeadm在虚拟机中创建伪多节点集群
date: 2023-05-08 00:10:20
tags:
---
# IP 地址定义
|节点名称| IP地址|
|---|---|
|ubuntu22-4-node0/master |192.168.123.20|
|ubuntu22-4-node1| 192.168.123.21|
|ubuntu22-4-node2 |192.168.123.22|

# 预准备初始环境

## 创建虚拟机实例
{% asset_img 1.jpg %}

使用Ubuntu22.4.2Iso镜像文件创建虚拟机
先创建一个实例，将初始化k8s集群前的预准备工作做完后使用虚拟机克隆功能复制为三个实例即可。

## 前置准备
```shell
# 配置节点名
$ sudo hostnamectl set-hostname ubuntu22-4-node0
$ sudo vim /etc/hosts
192.168.123.20 ubuntu22-4-node0
192.168.123.21 ubuntu22-4-node1
192.168.123.22 ubuntu22-4-node2
# 关闭swap; 注释/etc/fstab文件的最后一行
$ sudo sed -i '/swap/s/^/#/' /etc/fstab
# 开启IPv4转发
$ sudo cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

$ modprobe overlay
$ modprobe br_netfilter

$ sudo cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

$ sudo sysctl --system
```

## 安装containerd
```shell
$ curl -# -O  https://mirrors.aliyun.com/docker-ce/linux/ubuntu/dists/jammy/pool/stable/amd64/containerd.io_1.6.9-1_amd64.deb
$ sudo dpkg -i containerd.io_1.6.9-1_amd64.deb

#导出默认配置
$ sudo mkdir -p /etc/containerd/
$ sudo containerd config default > /etc/containerd/config.toml
#编辑配置文件
$ sudo vim /etc/containerd/config.toml

# 修改
[plugins]
  [plugins."io.containerd.grpc.v1.cri"]
    sandbox_image = "registry.aliyuncs.com/google_containers/pause:3.6"
    
# 配置 systemd cgroup 驱动 
[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
  ...
  [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
    SystemdCgroup = true

# 配置镜像加速
[plugins."io.containerd.grpc.v1.cri".registry]
      [plugins."io.containerd.grpc.v1.cri".registry.mirrors]
        [plugins. "io.contianerd.grpc.v1.cri".registry.mirrors."docker.io"]
          endpoint = ["https://registry.aliyuncs.com"]

:wq

# 开机自启/重启containerd服务
$ sudo systemctl enable containerd
$ sudo systemctl restart containerd
```
## 安装依赖组件
```shell
$ sudo apt-get install -y apt-transport-https ca-certificates curl
# 配置GPG文件
$ sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg  https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg
$ sudo echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] http://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
# 安装kubelet、kubeadm、kubectl
sudo apt-get update && sudo apt-get install -y kubelet=1.25.0-00 kubeadm=1.25.0-00 kubectl=1.25.0-00
# 标记软件包,固定版本
sudo apt-mark hold kubelet kubeadm kubectl
```

## 扩展虚拟机实例

> 以上操作完成后可以手动创建一个虚拟机快照，方便后边玩崩了回滚(可选)

使用PVE的虚拟机克隆功能将虚拟机实例`ubuntu22-4-node0`复制为三个实例。

复制完成后开机，修改主机名以及对应的ip地址
ubuntu22-4-node1 -> 192.168.123.21
ubuntu22-4-node2 -> 192.168.123.22


## 为三台虚拟机开启ssh互信（可选）
```shell
shubo@ubuntu-node2:~$ ssh-keygen
shubo@ubuntu-node2:~$ ssh-copy-id shubo@192.168.123.1x # 此操作在每个节点都执行一遍
```
# 使用kubeadm初始化集群
在 ubuntu22-4-node1/master 执行: 
```shell
$ sudo kubeadm init \
--kubernetes-version=v1.25.0 \
--image-repository registry.aliyuncs.com/google_containers \
--service-cidr=10.96.0.0/12 \
--pod-network-cidr=10.244.0.0/16 \
--cri-socket unix:///run/containerd/containerd.sock \
--v=5
```

初始化完成后会得到一个token
```shell
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.123.20:6443 --token l0qicb.ztul7i1t5ihrtb6b \
	--discovery-token-ca-cert-hash sha256:08d420cf2aa0984a81849933e31ea5ff5d09d7bc4327404f54d55fde16bd3e7e
```
在ubuntu22-4-node1,ubuntu22-4-node2 分别执行:
```shell
sudo kubeadm join 192.168.123.20:6443 --token l0qicb.ztul7i1t5ihrtb6b         --discovery-token-ca-cert-hash sha256:08d420cf2aa0984a81849933e31ea5ff5d09d7bc4327404f54d55fde16bd3e7e --cri-socket unix:///run/containerd/containerd.sock
```

# 安装网络插件flannel
此时集群初始化基本就完成了，通过`kubectl get nodes`命令可以看到node都处于not Ready的状态。这是因为还未安装任何网络插件。

安装flannel
```shell
$ wget https://ghproxy.com/https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml
$ sudo kubectl apply -f kube-flannel.yml
```

# cluster 初始化完成
```shell
shubo@ubuntu22-4-node0:~$ kubectl get nodes
NAME               STATUS   ROLES           AGE   VERSION
ubuntu22-4-node0   Ready    control-plane   54m   v1.25.0
ubuntu22-4-node1   Ready    <none>          43m   v1.25.0
ubuntu22-4-node2   Ready    <none>          42m   v1.25.0
```