# 字段

## 常用字段

### 必须存在的属性

|参数名| 字段类型 | 说明 |
|---  | --- | --- |
|version | string | k8s api 的版本，目前基本是v1,可以使用kubectl api-version命令查询
| kind | string | 这里指的是yaml文件定义的资源类型和角色,比如Pod
| metadate | object | 元数据对象，固定值写metadate
| metadate.name | String | 元数据对象的名字，这里由我们进行编写，比如命名pod的名字
| metadata.namespace | string | 元数据对象的命名空间，由我们定义
| spec | object | 详细定义对选哪个，固定值
| spec.containers[] | list | 这里是spec对象的容器列表的定义，是个列表
| spec.containers[].name | string | 定义容器的名字
| spec.containers[].image | string | 用到的镜像的名称

### spec主要对象

| 参数名 | 字段类型 | 说明 |
| --- | --- | --- |
| spec.containers[].name | string | 定义容器的名字
| spec.containers[].image | string | 定义要用到的镜像的名称
| spec.containers[].imagePullPolicy | string | 定义镜像拉取策略,有always,never, ifnotpersent三个值选择</br>1:always 每次尝试重新拉取镜像</br>2:never 表示仅使用本地镜像</br>3: ifnotpersent 本地有就用本地的没有就拉取线上的</br> 以上三个都不设置默认是always
| spec.containers[].command[] | List | 执行容器的启动命令，因为数组可以指定多个，不指定则使用镜像大宝时使用的启动命令
| spec.containers[].args[] | list | 指定容器启动命令参数，因为数组可以指定多个
| spec.containers[].workingDir | string | 指定容器的工作目录
| spec.containers[].volumeMounts[] | list | 指定容器内部的存储卷配置
| spec.containers[].vloumeMounts[].name | string | 指定可以被容器挂载的存储卷的名称
| spec.containers[].volumeMounts[].mountPath | string | 指定可以被容器挂载的容器卷的路径
| spec.containers[].volumeMounts[].readOnly | string | 设置存储卷的读写模式，默认为false
| spec.containers[].ports[] | list | 指定容器所用到的端口列表
| spec.containers[].ports[].name | string | 指定端口名称
| spec.containers[].ports[].containerPort | string | 指定容器需要监听的端口号
| spec.containers[].hostPort | string | 指定容器所在的主机需要监听的端口号，默认跟上面的containerPort相同，注意设置了hostPort同一台主机无法启动该容器的相同副本(因为主机的端口号不能相同，这样会冲突)
| spec.containers[].ports[].protocol | string | 指定端口协议，支持tcp, udp 默认为tcp
| spec.containers[].env[] | list | 指定容器运行时需要设置的环境变量列表
| spec.containers[].env[].name | string | 指定环境变量名称
| spec.containers[].env[].value | string | 指定环境变量的值
|spec.containers[].resources | obj | 指定资源限制和资源请求的值(设置容器的资源上限)
| spec.containers[].resources.limits | obj | 指定设置容器运行时资源的运行上限
| spec.containers[].resources.limits.cpu | string | 指定cpu的限制，单位为core，将用于docker run --cpu-shares参数
| spec.containers[].resources.limits.memory | string | 指定mem内存的限制，单位为mb, gb
| spec.containers[].resources.requests | obj | 指定容器启动和调度室的限制设置
| spec.containers[].resources.requests.cpu | string | cpu请求，单位core 容器启动时初始化可用的数量
| spec.containers[].resources.requests.memory | string | 内存请求，单位为mb gb 容器启动的初始化可用数量


### 额外的参数

| 参数名 | 类型 | 说明 |
| --- | --- | --- |
| spec.restartPoliy| string | 定义pod重启策略，可以选择：always,onfailure,默认Always</br>1: Always pod一单终止运行则无论容器是如何终止，kubelet都将重启它</br>2: OnFailure 只有pod以非零退出码终止时，kubelet才会重置改容器，如果容器正常结束将不会重启</br> 3 Never Pod终止后，kubelet将退出码报告给master不会重启改pod
| spec.nodeSelector | obj | 定义node的label过滤标签，以key:val格式指定
| spec.imagePullSecrets | obj | 定义pull镜像是使用secret名称,以name:secretkey格式指定
| spec.hostNetwork | bool | 定义是否使用主机网络模式，默认为false, true表示使用宿主机网络，不适用docker网桥，同时设置了true将无法在同一台宿主机上启动第二个副本
