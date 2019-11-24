# IO磁盘性能优化
#### IO基准测试
* fio常用测试命令
* direct 表示跳过系统缓存
* iodepth使用异步IO发出的IO请求上限
* rw 表示IO模式 read/write randread/randwrite
* ioengine sync libaio mmap net
* bs 表示IO的大小
* 

```
随机读
fio -name=randread -direct=1 -iodepth=64 -rw=randread -ioengine=libaio -bs=4k -size=1G -numjobs=1 -runtime=1000 -group_reporting -filename=/dev/sdb
# 随机写
fio -name=randwrite -direct=1 -iodepth=64 -rw=randwrite -ioengine=libaio -bs=4k -size=1G -numjobs=1 -runtime=1000 -group_reporting -filename=/dev/sdb
# 顺序读
fio -name=read -direct=1 -iodepth=64 -rw=read -ioengine=libaio -bs=4k -size=1G -numjobs=1 -runtime=1000 -group_reporting -filename=/dev/sdb
# 顺序写
fio -name=write -direct=1 -iodepth=64 -rw=write -ioengine=libaio -bs=4k -size=1G -numjobs=1 -runtime=1000 -group_reporting -filename=/dev/sdb 
```

#### 结果分析
```
Starting 1 process
Jobs: 1 (f=1): [r(1)][100.0%][r=80.2MiB/s,w=0KiB/s][r=20.5k,w=0 IOPS][eta 00m:00s]
randread: (groupid=0, jobs=1): err= 0: pid=12928: Wed Jan 30 09:25:22 2019
   read: IOPS=24.9k, BW=97.2MiB/s (102MB/s)(1024MiB/10535msec)
    slat (nsec): min=1439, max=11451k, avg=29995.24, stdev=67723.36
    clat (usec): min=2, max=18542, avg=2539.13, stdev=934.51
     lat (usec): min=28, max=18656, avg=2569.61, stdev=948.27
    clat percentiles (usec):
     |  1.00th=[  938],  5.00th=[ 1156], 10.00th=[ 1385], 20.00th=[ 1811],
     | 30.00th=[ 2180], 40.00th=[ 2474], 50.00th=[ 2671], 60.00th=[ 2802],
     | 70.00th=[ 2966], 80.00th=[ 3130], 90.00th=[ 3326], 95.00th=[ 3458],
     | 99.00th=[ 3982], 99.50th=[ 4948], 99.90th=[13566], 99.95th=[13829],
     | 99.99th=[18482]
   bw (  KiB/s): min=81304, max=137900, per=99.84%, avg=99371.14, stdev=17054.91, samples=21
   iops        : min=20326, max=34475, avg=24842.67, stdev=4263.69, samples=21
  lat (usec)   : 4=0.01%, 50=0.01%, 100=0.01%, 250=0.01%, 500=0.01%
  lat (usec)   : 750=0.02%, 1000=1.95%
  lat (msec)   : 2=22.61%, 4=74.44%, 10=0.70%, 20=0.27%
  cpu          : usr=0.59%, sys=93.35%, ctx=73394, majf=0, minf=71
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.1%, 32=0.1%, >=64=100.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.1%, >=64=0.0%
     issued rwt: total=262144,0,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=64

Run status group 0 (all jobs):
   READ: bw=97.2MiB/s (102MB/s), 97.2MiB/s-97.2MiB/s (102MB/s-102MB/s), io=1024MiB (1074MB), run=10535-10535msec

Disk stats (read/write):
  sdb: ios=257920/0, merge=109/0, ticks=173639/0, in_queue=173879, util=95.56%
```
* slat ，是指从 I/O 提交到实际执行 I/O 的时长（Submission latency）；
clat ，是指从 I/O 提交到 I/O 完成的时长（Completion latency）；
而 lat ，指的是从 fio 创建 I/O 到 I/O 完成的总时长。
* bw是吞吐量
* iops每秒I/O的次数
* blktrace /dev/sdb查看磁盘访问设备的I/O情况
* blkparse sdb -d sdb.bin

#### 应用程序优化
* 可以用追加写代替随机写，减少寻址开销，加快 I/O 写的速度。
* 可以借助缓存 I/O ，充分利用系统缓存，降低实际 I/O 的次数。
* 可以在应用程序内部构建自己的缓存，或者用 Redis 这类外部缓存系统
* 在需要频繁读写同一块磁盘空间时，可以用 mmap 代替 read/write，减少内存的拷贝次数。
* 在需要同步写的场景中，尽量将写请求合并，而不是让每个请求都同步写入磁盘，即可以用 fsync() 取代 O_SYNC
* 在多个应用程序共享相同磁盘时，为了保证 I/O 不被某个应用完全占用，推荐你使用 cgroups 的 I/O 子系统，来限制进程 / 进程组的 IOPS 以及吞吐量。

#### CFQ调度器优化
* ionice 来调整进程的 I/O 调度优先级
* Idle、Best-effort 和 Realtime

#### 文件系统优化
* xfs 支持更大的磁盘分区和更大的文件数量但是无法收缩
* 文件系统特性，日志模式，挂载选项 tune2fs //TODO
* 优化文件系统的缓存  
 * pdflush脏页的刷新频率 脏页的限额
 * 优化内核回收目录项缓存和索引节点缓存的倾向
 * 内存文件系统 tmpfs
 
#### 磁盘优化
* 使用RAID
* 选择调度算法
* 配置磁盘级别的隔离
* 顺序读的场景，调整磁盘预读  /sys/block/sdb/queue/read_ahead_kb  blockdev --setra 8192 /dev/sdb
* 优化内核块设备I/O的选项 /sys/block/sdb/queue/nr_requests
* 