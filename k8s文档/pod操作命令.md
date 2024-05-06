- 使用标签分类工作节点
- `kubectl get nodes xx gpu=true` 列出包含标签gpu=true的节点
- `kubectl get nodes -L gpu=true` 

- 将Pod调度到特定的节点 `nodeSelect`
- 获取崩溃容器的前一个日志 `kubectl logs mypod --previous`
- ReplicationController(简称RC) 监控pod状态如果发生故障会创建Pod的副本

- ReplicationController的三个部分
    - label selector (标签选择器) 用于确定ReplicationController作用域中有哪些pod
    - replica count (副本个数) 指定运行的pod数量
    - pod template(pod模板) 用于创建新的pod副本

![RC操作流程](https://raw.githubusercontent.com/Sunnnner/kws-images/master/20210125132759.png)


![RC的yaml定义](https://raw.githubusercontent.com/Sunnnner/kws-images/master/20210126114022.png)