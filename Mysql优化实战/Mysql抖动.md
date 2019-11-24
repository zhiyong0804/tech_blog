# Mysql抖动

##### 临时关闭binlog
set sql_log_bin=OFF;

#### 测试磁盘的IOPS
*  fio -filename=$filename -direct=1 -iodepth 1 -thread -rw=randrw -ioengine=psync -bs=16k -size=500M -numjobs=10 -runtime=10 -group_reporting -name=mytest
* 机械硬盘 IOPS 1045
* SATA固态 IOPS 6200
* M2.0固态 IOPS 13000

#### Mysql变慢的原因
* 正在flush刷新脏页面

#### 内存脏数据不淘汰的原因
* 一种是内存里存在，内存里就肯定是正确的结果，直接返回；
* 另一种是内存里没有数据，就可以肯定数据文件上是正确的结果，读入内存后返回。
这样的效率最高

#### 内存buffer pool
* 第一种是，还没有使用的；
* 第二种是，使用了并且是干净页；
* 第三种是，使用了并且是脏页。

#### 脏页比例的控制策略
* innodb_io_capacity 和磁盘的IOPS
* innodb_max_dirty_pages_pct 脏页比例上限

#### 脏页刷盘速度参考因素
* 脏页比例
* redo log写盘速度
* R = (脏页比例(max_dirty_pages_pact, (当前序列号 - checkpoint)N)
* 脏页刷新速度 = innodb_io_capacity  * R%
![cc44c1d080141aa50df6a91067475374](media/15445247066236/cc44c1d080141aa50df6a91067475374.png)
* 影响刷脏页的速度



#### 优化
* SSD不需要了 innodb_flush_neighbors = 0

#### 确定内存脏页面
* 每个数据页头部有LSN，8字节，每次修改都会变大。
* 对比这个LSN跟checkpoint 的LSN，比checkpoint小的一定是干净页
* flush是内存脏页直接过redo-log现在redo log 需要往前推进checkpoint，到LSN为12的这条log时，发现内存中的脏页列表里没有该页，且磁盘上该页的LSN也已经为12，则该页已刷脏，已为干净页，跳过。
* innodb_max_dirty_pages_pct 脏页比例

#### relog很小的后果
* io压力不大，但是数据库性能下降 

