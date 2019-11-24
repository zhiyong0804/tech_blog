# Socket网络通讯

#### 分配结构
* struct socket socket 是用于负责对上给用户提供接口，并且和文件系统关联
* struct sock  负责向下对接内核网络协议栈

#### 结构
* struct inet_connection_sock
* 处于各种状态的队列，各种超时时间、拥塞控制等字眼

#### accept
* 基于监听的socket创建新的struct socket
* 创建一个新的socket
* 创建一个新的struct file 和 fd关联到socket
* 如果 icsk_accept_queue 为空，则调用 inet_csk_wait_for_connect 进行等待
* 等待的时候，调用 schedule_timeout，让出 CPU，并且将进程状态设置为 TASK_INTERRUPTIBLE
* 如果再次 CPU 醒来，我们会接着判断 icsk_accept_queue 是否为空，同时也会调用 signal_pending 看有没有信号可以处理
* 一旦 icsk_accept_queue 不为空，就从 inet_csk_wait_for_connect 中返回，在队列中取出一个 struct sock 对象赋值给 newsk

#### connect函数
* ![c028381cf45d65d3f148e57408d26bd8](media/15630159070216/c028381cf45d65d3f148e57408d26bd8.png)
