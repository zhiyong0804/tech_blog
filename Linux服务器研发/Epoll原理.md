# Epoll原理
#### 常见问题
* Epoll有没有数量限制
* WebSocket推送的时候出现卡顿问题
* AIO为什么不能坐高性能网络IO
* 用户态到内核态交互的性能低

#### 问题
* 异步IO数据量太大会导致用户态溢出
* 信号驱动IO
* EPOLL_SIZE是指有数据返回的事件集合
* epoll_create1
* 