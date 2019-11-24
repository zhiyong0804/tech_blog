# sync.Cond

#### 作用
* 协调想要访问共享资源的协程的，用来通知呗互斥锁阻塞的线程

#### cond work witch mutex
* sync.Cond不是开箱即用的，需要初始化

#### question
* *sync.Cond类型的值可以被传递吗？那sync.Cond呢
* 