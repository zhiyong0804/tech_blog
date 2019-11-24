# epoll原理

#### 集合
* A 红黑树IO
* B 双向链表
* C 所有IO的集合
* A = C(包含B)

#### epoll
* epi->event.events |= event;表示有两个事件
* pthread_cond_signal(&ep->cond)通知触发epoll_wait向下执行
* 0 不挂起
* -1 挂起
* 正数 等待时间