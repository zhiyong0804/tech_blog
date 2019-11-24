# Linux buffer和cache
#### buffer和cache
* Buffers 是内核缓冲区用到的内存，对应的是 /proc/meminfo 中的 Buffers 值。
* Cache 是内核页缓存和 Slab 用到的内存，对应的是 /proc/meminfo 中的 Cached 与 SReclaimable 之和。
* Buffers 是对原始磁盘块的临时存储，也就是用来缓存磁盘的数据，通常不会特别大（20MB 左右）。这样，内核就可以把分散的写集中起来，统一优化磁盘的写入，比如可以把多次小的写合并成单次大的写等等。
* Cached 是从磁盘读取文件的页缓存，也就是用来缓存从文件读取的数据。这样，下次访问这些文件数据时，就可以直接从内存中快速获取，而不需要再次访问缓慢的磁盘。
* SReclaimable 是 Slab 的一部分。Slab 包括两部分，其中的可回收部分，用 
* SReclaimable 记录；而不可回收部分，用 SUnreclaim 记录。

#### 结论
* Buffer 既可以用作“将要写入磁盘数据的缓存”，也可以用作“从磁盘读取数据的缓存”。
* Cache 既可以用作“从文件读取数据的页缓存”，也可以用作“写文件的页缓存”。

#### 思考
* 如何统计出所有进程的物理内存使用量呢？

#### 利用缓存来提升效率
* 缓存命中率
* cachestat 提供了整个系统缓存的读写命中情况。
* cachetop 提供了每个进程的缓存命中情况。
* strace -p $(pgrep app) 查看进行的系统调用
* 