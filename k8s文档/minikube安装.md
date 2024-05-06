* 首先检查是否支持vm虚拟化`grep -E --color 'vmx|svm' /proc/cpuinfo
`输出为空则不支持
## linux安装minikube
* `curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 \
  && chmod +x minikube`下载并赋予权限root权限下
* `sudo mkdir -p /usr/local/bin`
* `sudo install minikube /usr/local/bin/`

## Mac下安装
* `brew cask install minikube
`
* `curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-amd64 \
  && chmod +x minikube`
* 添加到环境变量`sudo mv minikube /usr/local/bin
`

## 启动

`minikube start`

## 错误就退出`mach does not exist`

## 删除minikube`minikube delete`