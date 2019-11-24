# Redis 性能调优

#### 问题描述
* Can't save in background: fork: Cannot allocate memory
* maxmemory10 G-，这个选项非常重要，它用来指定 Redis 可使用的最大内存，Redis 在启动时会把数据加载到内存中，达到最大内存后，Redis 会先尝试清除已到期或即将到期的 Key，当此方法处理后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。

#### /proc/sys/vm/overcommit_memory
* 0, 默认值，表示内核将检查是否有足够的可用内存供应用进程使用；如果有足够的可用内存，内存申请允许；否则，内存申请失败，并把错误返回给应用进程。
* 1, 表示内核允许分配所有的物理内存，而不管当前的内存状态如何
* 2, 表示内核允许分配超过所有物理内存和交换空间总和的内存。

#### 问题解决
* 我们操作系统物理内存是 16 G, redis 主进程占用了最大 10 G 内存，而 fork 子进程也需要 10 G 的内存，这样，redis 总共就需要 20 GB 内存了，而操作系统只有 16 GB 物理内存，肯定会报内存无法分配。
* 修改maxmemory为小于物理内存的一半
* 增加服务器物理内存

#### redis
* config get auto-aof-rewrite-min-size 
* config get auto-aof-rewrite-percentage
* 