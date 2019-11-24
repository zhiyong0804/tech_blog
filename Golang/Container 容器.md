# Container 容器
#### List
* 为什么链表可以做到开箱即用
* 延迟初始化
* 通过PushFront,PushBack,PushBackList,PushFrontList来判断链表的状态

#### Ring和List的区别
* Ring的Len的时间复杂度是O(N)
* List的Len方法的算法复杂度是O(1)
* var r ring.Ring 声明一个长度为1的循环链表，List则是长度为0的链表
* Ring的长度创建后就不会改变，List不会

#### ring的循环链表适用场景
* list是构建FIFO队列
* ring是作网页轮播