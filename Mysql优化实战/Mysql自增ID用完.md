# Mysql自增ID用完
#### 表定义的自增ID
* 表定义的自增值达到上限后的逻辑是：再申请下一个 id 时，得到的值保持不变。

#### InnoDB 系统自增 row_id
* InnoDB维护全局的dict_sys.row_id

#### Xid
* redo log 和 binlog 相配合的时候，一个共同的字段叫作 Xid
* Mysql内部维护global_query_id赋值给query_id,然后把query_id赋值给Xid
*  MySQL 重启之后会重新生成新的 binlog 文件，这就保证了，同一个 binlog 文件里，Xid 一定是惟一的

#### innodb trx_id
* InnoDB 内部维护了一个 max_trx_id 全局变量，每次需要申请一个新的 trx_id 时，就获得 max_trx_id 的当前值，然后并将 max_trx_id 加 1
* trx_id不止加1
* update 和 delete 语句除了事务本身，还涉及到标记删除旧数据，也就是要把数据放到 purge 队列里等待后续物理删除，这个操作也会把 max_trx_id+1， 因此在一个事务中至少加 2；
* InnoDB 的后台操作，比如表的索引信息统计这类操作，也是会启动内部事务的，因此你可能看到，trx_id 值并不是按照加 1 递增的。
* 只读查询的trx_id=trx变量指针2^48
* 好处
* 一个好处是，这样做可以减小事务视图里面活跃事务数组的大小
* 另一个好处是，可以减少 trx_id 的申请次数

#### thread_id
* 系统保存了一个全局变量 thread_id_counter，每新建一个连接，就将 thread_id_counter 赋值给这个新连接的线程变量。
* thread_id_counter 定义的大小是 4 个字节，因此达到 232-1 后，它就会重置为 0

#### 总结
* 表的自增 id 达到上限后，再申请时它的值就不会改变，进而导致继续插入数据时报主键冲突的错误。
* row_id 达到上限后，则会归 0 再重新递增，如果出现相同的 row_id，后写的数据会覆盖之前的数据。
* Xid 只需要不在同一个 binlog 文件中出现重复值即可。虽然理论上会出现重复值，但是概率极小，可以忽略不计。
* InnoDB 的 max_trx_id 递增值每次 MySQL 重启都会被保存起来，所以我们文章中提到的脏读的例子就是一个必现的 bug，好在留给我们的时间还很充裕。
* thread_id 是我们使用中最常见的，而且也是处理得最好的一个自增 id 逻辑了

