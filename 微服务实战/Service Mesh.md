# Service Mesh
#### Service Mesh原理

#####SideCar
![daa8dee489e51238f450b51a53c1016d](media/15547108978976/daa8dee489e51238f450b51a53c1016d.png)
* 基于 iptables 的网络拦截
* 采用协议转换的方式

#####Control Plane
* Control Plane
* 服务发现。服务提供者会通过 SideCar 注册到 Control Plane 的注册中心，这样的话服务消费者把请求发送给 SideCar 后，SideCar 就会查询 Control Plane 的注册中心来获取服务提供者节点列表。
* 负载均衡。SideCar 从 Control Plane 获取到服务提供者节点列表信息后，就需要按照一定的负载均衡算法从可用的节点列表中选取一个节点发起调用，可以通过 Control Plane 动态修改 SideCar 中的负载均衡配置。
* 请求路由。SideCar 从 Control Plane 获取的服务提供者节点列表，也可以通过 Control Plane 来动态改变，比如需要进行 A/B 测试、灰度发布或者流量切换时，就可以动态地改变请求路由。
* 故障处理。服务之间的调用如果出现故障，就需要加以控制，通常的手段有超时重试、熔断等，这些都可以在 SideCar 转发请求时，通过 Control Plane 动态配置。
* 安全认证。可以通过 Control Plane 控制一个服务可以被谁访问，以及访问哪些信息。
* 监控上报。所有 SideCar 转发的请求信息，都会发送到 Control Plane，再由 Control Plane 发送给监控系统，比如 Prometheus 等。
* 日志记录。所有 SideCar 转发的日志信息，也会发送到 Control Plane，再由 Control Plane 发送给日志系统，比如 Stackdriver 等。![746ba12fff2b184157b00ac44ec2e862](media/15547108978976/746ba12fff2b184157b00ac44ec2e862.png)

