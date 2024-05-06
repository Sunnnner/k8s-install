* `LabelSelector`服务所针对的Pod集合
* `ServiceSpec`通过type在ServiceSpec中指定可以以不同的方式公开服务
* `ClusterIP`默认）-在群集的内部IP上公开服务。这种类型使得只能从群集内访问服务
* `NortPort` 使用NAT在群集中每个选定节点的相同端口上公开服务。使用可以从群集外部访问服务<NodeIP>:<NodePort>。ClusterIP的超集
* `LoadBalancer` 在当前云中创建一个外部负载平衡器（如果支持），并为该服务分配一个固定的外部IP。NodePort的超集
* `ExternalName`externalName通过返回具有该名称的CNAME记录，使用任意名称（在规范中指定）公开服务。不使用代理。此类型需要v1.7或更高版本kube-dns
* 