# 无法Kill的语句
#### kill
* kill query
* kill connection

#### kill query执行原理
* 把 session B 的运行状态改成 THD::KILL_QUERY(将变量 killed 赋值为 THD::KILL_QUERY)；
* 给 session B 的执行线程发一个信号。让session退出等待，处理THD:KILL_QUERY
* 等行锁时，使用的是 pthread_cond_timedwait 函数，这个等待状态可以被唤醒,等待进入InnoDB的循环过程中，没有去判断线程的状态，所以不会进入逻辑终止阶段
*  pthread_cond_timedwait 函数，这个等待状态可以被唤醒
*  如果没有判断线程的状态，就不会被唤醒

#### kill connection
* 把 12 号线程状态设置为 KILL_CONNECTION；
* 关掉 12 号线程的网络连接。
* 线程依然是killed状态，直到满足进入InnoDB条件后，才会判断线程状态，最后进入终止逻辑状态

#### kill无效
* 线程没有执行到判断线程状态的逻辑
* 终止逻辑耗时比较长
* 超大事物被kill
* 大查询回滚
* DDL命令执行到最后阶段

#### 客户端
* 连接后操作
* 执行 show databases；
* 切到 db1 库，执行 show tables；
* 把这两个命令的结果用于构建一个本地的哈希表。

#### 客户端查询返回结果
* 一种是本地缓存，也就是在本地开一片内存，先把结果存起来。API 开发，对应的就是 mysql_store_result 方法。
* 另一种是不缓存，读一个处理一个。API开发，对应的就是 mysql_use_result 方法。-quick参数

#### quick参数效果
* 第一点，就是前面提到的，跳过表名自动补全功能。
* 第二点，mysql_store_result 需要申请本地内存来缓存查询结果，如果查询结果太大，会耗费较多的本地内存，可能会影响客户端本地机器的性能；
* 第三点，是不会把执行命令记录到本地的命令历史文件。

#### kill query本质
* 这些“kill 不掉”的情况，其实是因为发送 kill 命令的客户端，并没有强行停止目标线程的执行，而只是设置了个状态，并唤醒对应的线程。而被 kill 的线程，需要执行到判断状态的“埋点”，才会开始进入终止逻辑阶段。并且，终止逻辑本身也是需要耗费时间的