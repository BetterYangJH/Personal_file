* kubernetes,基于容器的集群管理平台

* 优点

    * 简化应用部署
    * 提高硬件资源利用率
    * 健康检查和自修复
    * 自动扩容缩容
    * 服务发现和负载均衡

* 结构

    ```python
    K8S集群－Master主节点：控制和管理整个集群系统的控制面板
    		Node工作节点：运行实际应用
    主节点
    －etcd　保存集群状态
    －API Controller 对外接口
    －Scheduler 对内部的资源进行调度
    －Controller Manager 管理控制器
    工作节点
    －Kubelet　监视指派到所在Node上的Pod，包括增删改查
    －Kube-proxy　为Pod对象提供代理
    －Fluentd　日志收集、存储与查询
    －pod（最小调度单元）－Docker容器
    # pod不会跨越多个工作节点
    # 一个pod包含一组容器
    # pod相当与逻辑主机，每个pod都有自己的 IP 地址pod内的容器共享相同的 IP 和端口空间
    # 默认情况下，每个容器的文件系统与其他容器完全隔离
    ```