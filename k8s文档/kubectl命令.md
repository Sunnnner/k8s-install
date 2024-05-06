* `kubectl cluster-info`查看运行主机
* `kubectl get nodes`查看节点
* `kubectl get deployments`查看部署app
* `kubectl create`创建pod
* `kubectl get pods` 查看创建的pod
* `kubectl get events`查看集群事件
* `kubectl config view`查看kubectl 配置
* `kubectl describe pods`查看Pod容器的详细信息
* `kubectl get services` 查看所有服务
* `kubectl describe deplpyment` 查看部署详细信息
* 
* `kubectl expose deployment/kubernetes-bootcamp --type='NodePort' --port 8080`开启外部访问模式为NodePort内部端口为8080外部端口使用kubectl get services查看
* `kubectl describe services/kubernetes-bootcamp` 查看打开端口服务详细信息
* `export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')`获取打开的外部端口号并添加环境变量
* `echo NODE_PORT=$NODE_PORT`获取环境变量
* `curl $(minikube ip):$NODE_PORT` 测试节点暴露在外部的接口
* 
* `kubectl expose deployment hello-node --type=LoadBalancer --port=8080`将创建的pod公开到互联网
* `kubectl proxy` 本机访问k8s
* `--type=LoadBalancer`表示指示要在集群外公开你的Pod服务
* `kubectl get services`查看刚刚创建的服务(刚公开的pods服务)
* `kubectl get pod, svc -n kube-system`查看刚刚创建的pod和服务
* `kubectl create deployment appname --image=docker_image_url:版本号`创建部署app
* `export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')` 将应用名称保存到环境变量 `$POD_NAME`是应用名称
* `curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME/proxy/` Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-5b48cfdcbd-mcwxs | v=1
* `kubectl logs $POD_NAME`查看容器日志
* `kubectl exec $POD_NAME env`查看容器参数
* `kubectl exec -ti $POD_NAME bash`开启容器bash会话(进入容器neibu)
* 

## 清理Pods
* `kubectl delete service hello-node`删除服务
* `kubectl delete deployment hello-node`删除部署

## 启用插件minikube
* `minikube addons list`查看支持插件
* `minikube addons enable heapster`启用插件
* `minikube addons disable heapster`禁用插件
* `minikube stop`停止虚拟机
* `minikube delete`删除虚拟机
* 