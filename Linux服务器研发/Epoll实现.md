# Epoll实现
#### 问题
* 双向链表和红黑树的线程安全
* spinlock

#### 时间
* struct timespec 秒和纳秒

#### ET和LT的触发
* epoll_callback
* 可读
* 可写
* 对端关闭
* 建立连接
* payload>0 会不停的触发回调 水平触发
* 只调用一次