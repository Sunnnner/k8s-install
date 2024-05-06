# kubeadm 简介
- kubeadm 是 k8s 官方提供的用于快速部署 k8s 集群的命令行工具，也是官方推荐的最小化部署 k8s 集群的最佳实践，比起直接用二进制部署能省去很多工作，因为该方式部署的集群的各个组件以 docker 容器的方式启动，而各个容器的启动都是通过该工具配自动化启动起来的。

- kubeadm 不仅仅能部署 k8s 集群，还能很方便的管理集群，比如集群的升级、降级、集群初始化配置等管理操作。

- kubeadm 的设计初衷是为新用户提供一种便捷的方式来首次试用 Kubernetes， 同时也方便老用户搭建集群测试他们的应用。

## kubeadm 的使用案例：

* 新用户可以从 kubeadm 开始来试用 Kubernetes。
* 熟悉 Kubernetes 的用户可以使用 kubeadm 快速搭建集群并测试他们的应用。
* 大型的项目可以将 kubeadm 和其他的安装工具一起形成一个比较复杂的系统。
## 安装环境要求
- 一台或多台运行着下列系统的机器:
```
Ubuntu 16.04+
Debian 9
CentOS 7
RHEL 7
Fedora 25/26
HypriotOS v1.0.1+
Container Linux (针对1800.6.0 版本测试)
```
- 每台机器 2 GB 或更多的 RAM (如果少于这个数字将会影响您应用的运行内存)
- 2 CPU 核心或更多(节点少于 2 核的话新版本 kubeadm 会报错)
- 集群中的所有机器的网络彼此均能相互连接(公网和内网都可以)
- 禁用 Swap 交换分区。（Swap 分区必须要禁掉，否则安装会报错）


- `kubeadm init --kubernetes-version v1.13.2 --image-repository registry.aliyuncs.com/google_containers --pod-network-cidr=10.244.0.0/16 --apiserver-advertise-address=192.168.9.150`
## 参数说明：
* `--kubernetes-version`: 安装指定版本的 k8s 版本，该参数和 kubeadm 的版本有关，特定版本的 kubeadm 并不能安装所有版本的 k8s，最好还是 kubeadm 的版本和该参数指定的版本一致。

* `--image-repository`: 该参数仅在高版本（具体哪个版本没仔细查，反正在 1.13.x 中是支持的）的 kubeadm 中支持，用来设置 kubeadm 拉取 k8s 各组件镜像的地址，默认拉取的地址是：k8s.gcr.io。众所周知 k8s.gcr.io 国内是无法访问的，所以在这里改为阿里云镜像仓库。

* `--pod-network-cidr`: 设置 pod ip 的网段 ，网段之所以是 10.244.0.0/16，是因为后面安装 flannel 网络插件时，yaml 文件里面的 ip 段也是这个，两个保持一致，不然可能会使得 Node 间 Cluster IP 不通。这个参数必须得指定，如果这里不设置的话后面安装 flannel 网络插件时会报如下错误：

`E0317 17:02:15.077598       1 main.go:289] Error registering network: failed to acquire lease: node "k8s-master" pod cidr not assigned`
- `--apiserver-advertise-address`: API server 用来告知集群中其它成员的地址，这个参数也必须得设置，否则 api-server 容器启动不起来，该参数的值为 master 节点所在的本地 ip 地址。

> 题外话：像之前没有 `--image-repository` 这个参数时，大家为了通过 kubeadm 安装 k8s 都是采用”曲线救国”的方式：先从别的地方把同样的镜像拉到本地（当然镜像的 tag 肯定不是 k8s.gcr.io/xxxx），然后将拉下来的镜像重新打个 tag，tag 命名成和执行 kubeadm init 时真正拉取镜像的名称一致（比如：k8s.gcr.io/kube-controller-manager-amd64:v1.13.2）。这么做显然做了很多不必要的工作，幸好现在有了 --image-repository 这个参数能自定义 kubeadm 拉取 k8s 相关组件的镜像地址了。

- 执行上面命令后，如果出现如下输出（截取了部分），则表示 master 节点安装成功了：
```
...
[bootstraptoken] creating the "cluster-info" ConfigMap in the "kube-public" namespace
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

Your Kubernetes master has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

You can now join any number of machines by running the following on each node
as root:

  kubeadm join 192.168.10.100:6443 --token jm6o42.9ystvjarc6u09pjp --discovery-token-ca-cert-hash sha256:64405f3a90597e0ebf1f33134649196047ce74df575cb1a7b38c4ed1e2f94421
```

- 根据上面输出知道：要开始使用集群普通用户执行下面命令：
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

- 现在就可以使用 kubectl 访问集群了：
```
[vagrant@k8s-master ~]$ kubectl get node
NAME         STATUS     ROLES    AGE   VERSION
k8s-master   NotReady   master   13m   v1.13.2
```
- 可以看出现在 master 节点还是 NotReady 状态，这是因为默认情况下，为了保证 master 的安全，master 是不会被分配工作负载的。你可以取消这个限制通过输入（不建议这样做，我们后面会向集群中添加两 node 工作节点）：

- `$ kubectl taint nodes --all node-role.kubernetes.io/master-`
## 安装 Pod 网络插件（CNI）
- Pod 网络插件有很多种，具体见这里：`https://kubernetes.io/docs/concepts/cluster-administration/addons/`，我们选择部署 Flannel 网络插件：

`kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml`