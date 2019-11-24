# kafka请求

#### 网络架构
* ![e1ae8884999175dac0c6e21beb2f7e6e](media/15642233189788/e1ae8884999175dac0c6e21beb2f7e6e.png)
* ![d8a7d6f0bdf9dc3af4ff55ff79b42068](media/15642233189788/d8a7d6f0bdf9dc3af4ff55ff79b42068.png)
* io线程池的num.io.threads
* 请求队列是所有网络线程共享的，而响应队列则是每个网络线程专属的

#### Purgatory
* 延时请求，就是那些一时未满足条件不能立刻处理的请求

#### 控制类请求
*  PRODUCE 和 FETCH 这类请求称为数据类请求，把 LeaderAndIsr、StopReplica 这类请求称为控制类请求
*  网络线程池和 IO 线程池，它们分别处理数据类请求和控制类请求