# k8s 新版搭建方式

## 准备

- 树莓派无法作为master
- debian10系统
- 确保 iptables 工具不使用 nftables 后端

```shell
sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
sudo update-alternatives --set arptables /usr/sbin/arptables-legacy
sudo update-alternatives --set ebtables /usr/sbin/ebtables-legacy
```

- 设置主机名称`sudo hostnamectl set-hostname node1`

- 添加hosts

> 不确定是否为必须

```shell
cat << EOF | sudo tee -a /etc/hosts

192.168.1.20 node001
192.168.1.21 node002
192.168.1.22 node003

EOF
```

## 禁用 swap 分区

> 没有找到永久禁用方法采用 sudo swapoff - a进行临时禁用

## 为所有集群安装docker、kubeadm

### docker insall

```bash
sudo apt-get install apt-transport-https  ca-certificates software-properties-common

curl -fsSL https://get.docker.com -o get-docker.sh
sudo chmod +x get-docker.sh 
sh get-docker.sh --mirror Aliyun
```
- docker 权限问题

```bash
sudo groupadd docker #添加docker用户组
sudo gpasswd -a ${USER} docker #将登陆用户加入到docker用户组中
newgrp docker #更新用户组
```

- 设置docker 驱动

```shell
cat << EOF | sudo tee -a /etc/docker/daemon.json
{
    "registry-mirrors": ["https://nu3jg306.mirror.aliyuncs.com"],
    "exec-opts": ["native.cgroupdriver=systemd"]
}
EOF
```
> sudo systemctl daemon-reload&&sudo systemctl restart docker

### kubeadm install

```bash
apt-get update && apt-get install -y apt-transport-https
curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add - 
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
EOF  
apt-get update
apt-get install -y kubelet kubeadm kubectl
```

- 防止更新`sudo apt-mark hold kubelet kubeadm kubectl`


## 获取镜像

- 因为国内访问不到k8s.io我们使用脚本使用阿里云进行下载然后重新打tag

- 首先查看版本信息`kubeadm config images list`

``` bash
k8s.gcr.io/kube-apiserver:v1.19.3
k8s.gcr.io/kube-controller-manager:v1.19.3
k8s.gcr.io/kube-scheduler:v1.19.3
k8s.gcr.io/kube-proxy:v1.19.3
k8s.gcr.io/pause:3.2
k8s.gcr.io/etcd:3.4.13-0
k8s.gcr.io/coredns:1.7.0
```

### 为所有node节点安装

```bash
#!/bin/bash
images=(
    kube-proxy:v1.19.3
    pause:3.2
    coredns:1.7.0
)

for imageName in ${images[@]} ; do
    docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/$imageName
    docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/$imageName k8s.gcr.io/$imageName
done
```

### master节点安装

```bash
#!/bin/bash
images=(
    kube-apiserver:v1.19.3
    kube-controller-manager:v1.19.3
    kube-scheduler:v1.19.3
    kube-proxy:v1.19.3
    pause:3.2
    etcd:3.4.13-0
    coredns:1.7.0
)

for imageName in ${images[@]} ; do
    docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/$imageName
    docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/$imageName k8s.gcr.io/$imageName
done

```

- 安装完毕后手动删除阿里云tag

## 初始化master

```shell
sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --service-cidr=10.192.0.0/16 
```
> 如果使用 flannel 网络，--pod-network-cidr=10.244.0.0/16 参数是必须的

## 配置 kubectl

- 在 Master 节点执行：

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

> 执行完之后，需要退出重新登陆一次

## node加入集群

```bash
sudo kubeadm join 192.168.1.100:6443 --token kig853.h9ztt539c8a50sdg \
    --discovery-token-ca-cert-hash sha256:88418c67e5f4425fb67f5cca30dc110d86026b568e8ef19c627352464f4e0eb2
```
> 默认token24小时 过期后执行`sudo kubeadm token create --print-join-command`

## 安装 flannel 网络插件

- 在master执行

```shell
mkdir ~/k8s && cd k8s
touch kube-flannel.yml
# 浏览器访问`https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml`
# copy content -> kube-flannel.yml
kubectl apply -f kube-flannel.yml
```

### 添加自动补全

```bash
cat << EOF | tee -a ~/.bashrc 

source <(kubectl completion bash)

EOF
```