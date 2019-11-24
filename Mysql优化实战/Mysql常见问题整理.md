# Mysql常见问题整理
#### 那么在什么场景下，一天一备会比一周一备更有优势呢？或者说，它影响了这个数据库系统的哪个指标？
* RTO恢复目标时间
* 数据库的备份周期影响恢复速度

#### 怎么避免长事物
##### 开发端
* 确认是否使用了set autocommit=1。
* 确认是否有不必要的只读事务。
* 通过 SET MAX_EXECUTION_TIME 命令，来控制每个语句执行的最长时间，避免单个语句意外执行太长时间。

##### 数据库端
* 监控 information_schema.Innodb_trx 表，设置长事务阈值，超过就报警 / 或者 kill；
* Percona 的 pt-kill
* 在业务功能测试阶段要求输出所有的 general_log，分析日志行为提前发现问题；

#### 重建索引
##### 好处
* 索引可能因为删除，或者页分裂等原因，导致数据页有空洞，重建索引的过程会创建一个新的索引，把数据按顺序插入，这样页面的利用率最高，也就是索引更紧凑、更省空间
* InnoDB虽然删除了表的部分记录,但是它的索引还在, 并未释放只能是重新建表才能重建索引
* 重建二级索引可以先drop再create
* 重建主键索引alter table T engine=InnoDB

#### change buffer引发的性能问题
* 某个业务的库内存命中率突然从 99% 降低到了 75%，整个系统处于阻塞状态，更新语句全部堵住
* 这个业务有大量插入数据的操作，而他在前一天把其中的某个普通索引改成了唯一索引
* 查询性能没有差别，针对更新性能而言,普通索引更新性能会更快一点

##### 针对机械硬盘的优化
* 使用普通索引
* 把change buffer开大

#### 表事物更新不可见的问题
* Innodb 要保证这个规则：事务启动以前所有还没提交的事务，它都不可见。
* 但是只存一个已经提交事务的最大值是不够的。 因为存在一个问题，那些比最大值小的事务，之后也可能更新
* 所以事务启动的时候还要保存“现在正在执行的所有事物ID列表”，如果一个row trx_id在这列表中，也要不可见。

#### 联合主键
```
CREATE TABLE `geek` (
  `a` int(11) NOT NULL,
  `b` int(11) NOT NULL,
  `c` int(11) NOT NULL,
  `d` int(11) NOT NULL,
  PRIMARY KEY (`a`,`b`),
  KEY `c` (`c`),
  KEY `ca` (`c`,`a`),
  KEY `cb` (`c`,`b`)
) ENGINE=InnoDB;

```
* ca 可以去掉，因为c的索引的组织结构和ca一模一样，cb要保留，二级索引在面对联合主键的时候需要把联合主键的数据都加上


#### MySQL 怎么知道 binlog 是完整的?
* statement 格式的 binlog，最后会有 COMMIT；
* row 格式的 binlog，最后会有一个 XID event。

#### 处于 prepare 阶段的 redo log 加上完整 binlog，重启就能恢复，MySQL 为什么要这么设计?
* 在时刻 B，也就是 binlog 写完以后 MySQL 发生崩溃，这时候 binlog 已经写入了，之后就会被从库（或者用这个 binlog 恢复出来的库）使用。
* binglog不支持页界别的数据

#### 业务上有这样的需求，A、B 两个用户，如果互相关注，则成为好友。设计上是有两张表，一个是 like 表，一个是 friend 表，like 表有 user_id、liker_id 两个字段，我设置为复合唯一索引即 uk_user_id_liker_id。语句执行逻辑是这样的

####  sync_binlog=0 的时候，还是可以看到 binlog 文件马上做了修改。
* 数据写到了page cache，就是文件系统的 page cache。而你用 ls 命令看到的就是文件系统返回的结果。

#### 准确定位binlog
* stop-datetime

#### Mysql中间件
* 网易cetus
* myscat
* canal输出binlog到hadoop

#### 双 M 结构会出现循环复制
* 在一个主库更新事务后，用命令 set global server_id=x 修改了 server_id。等日志再传回来的时候，发现 server_id 跟自己的 server_id 不同，就只能执行了。
* binlog 从B传给A, A和A'构成双M结构
* 终止循环复制

```
stop slave；
CHANGE MASTER TO IGNORE_SERVER_IDS=(server_id_of_B);
start slave;
stop slave；
CHANGE MASTER TO IGNORE_SERVER_IDS=();
start slave;
```
#### 主从切换导致主的最新数据没有同步到从
* 使用semi-sync
* 启动后业务做数据对账

####  GTID 模式下设置主从关系的时候，从库执行 start slave 命令后，主库发现需要的 binlog 已经被删除掉了，导致主备创建不成功。这种情况下，你觉得可以怎么处理呢？
* 如果业务允许主从不一致的情况那么可以在主上先show global variables like 'gtid_purged';然后在从上执行set global gtid_purged =' '.指定从库从哪个gtid开始同步,binlog缺失那一部分,数据在从库上会丢失,就会造成主从不一致
* 需要主从数据一致的话,最好还是通过重新搭建从库来做。
* 如果有其它的从库保留有全量的binlog的话，可以把从库指定为保留了全量binlog的从库为主库(级联复制)
* 如果binlog有备份的情况,可以先在从库上应用缺失的binlog,然后在start slave

#### 一主多从等 GTID 的方案，现在你要对主库的一张大表做 DDL，可能会出现什么情况呢？为了避免这种情况，你会怎么做呢？

#### 如果一个事务被 kill 之后，持续处于回滚状态，从恢复速度的角度看，你是应该重启等它执行结束，还是应该强行重启整个 MySQL 进程。
* 因为重启之后该做的回滚动作还是不能少的，所以从恢复速度的角度来说，应该让它自己结束。
* 如果这个语句可能会占用别的锁，或者由于占用 IO 资源过多，从而影响到了别的语句执行的话，就需要先做主备切换，切到新主库提供服务

#### 如果客户端由于压力过大，迟迟不能接收数据，会对服务端造成什么严重的影响。
* 如果前面的语句有更新，意味着它们在占用着行锁，会导致别的语句更新被锁住；
* 当然读的事务也有问题，就是会导致 undo log 不能被回收，导致回滚段空间膨胀。

#### 使用 Block Nested-Loop Join(BNL) 算法时，可能会对被驱动表做多次扫描。如果这个被驱动表是一个大的冷数据表，除了会导致 IO 压力大以外，还会对系统有什么影响呢？
* 由于我们的 join 语句在循环读磁盘和淘汰内存页，进入 old 区域的数据页，很可能在 1 秒之内就被淘汰了。这样，就会导致这个 MySQL 实例的 Buffer Pool 在这段时间内，young 区域的数据页没有被合理地淘汰。
* Buffer Pool 的影响就是持续性的，需要依靠后续的查询请求慢慢恢复内存命中率。
* 临时补救措施就是join_buffer_size的值增大
* ***主要影响点***
* 可能会多次扫描被驱动表，占用磁盘 IO 资源；
* 判断 join 条件需要执行 M*N 次对比（M、N 分别是两张表的行数），如果是大表就会占用非常多的 CPU 资源；
* 可能会导致 Buffer Pool 的热数据被淘汰，影响内存命中率。

#### 维护的 MySQL 系统里有内存表，怎么避免内存表突然丢数据，然后导致主备同步停止的情况。
* 那么就把备库的内存表引擎先都改成 InnoDB。对于每个内存表，执行
* set sql_log_bin=off;
* alter table tbl_name engine=innodb;
* 可以配置一个自动巡检的工具，在备库上发现内存表就把引擎改了。

#### MySQL 解析 statement 格式的 binlog 的时候，对于 load data 命令，解析出来为什么用的是 load data local
* 为了确保备库应用 binlog 正常。因为备库可能配置了 secure_file_priv=null，所以如果不用 local 的话，可能会导入失败，造成主备同步延迟。
* 应用场景是使用 mysqlbinlog 工具解析 binlog 文件，并应用到目标库，就可以支持非本地的host

#### 怎么给分区表创建自增主键
* (ftime,id) 使用前缀索引的规则减少索引

#### changebuffer 异常断电情况下是否会数据丢失
* 是只更新内存，但是在事务提交的时候，我们把 change buffer 的操作也记录到 redo log 里了，所以崩溃恢复的时候，change buffer 也能找回来

#### mysql扫描行数变大的情况
* 由于mysql是使用标记删除来删除记录的,并不从索引和数据文件中真正的删除。
如果delete和insert中间的间隔相对较小,purge线程还没有来得及清理该记录。
如果主键相同的情况下,新插入的insert会沿用之前删除的delete的记录的空间。
由于相同的数据量以及表大小,所以导致了统计信息没有变化
* 为什么开启了session A,session B扫描行数变成3W
由于session A开启了一致性读,目的为了保证session A的可重复读,insert只能
另起炉灶,不能占用delete的空间。所以出现的情况就是delete虽然删除了,但是
未释放空间,insert又增加了空间。导致统计信息有误

#### 给学号字段创建索引
* 使用数字来存9位数字的入学年份

#### 如果一个高配的机器，redo log 设置太小，会发生什么情况。
* 磁盘压力很小，但是数据库出现间歇性的性能下跌。
* SSD性能很好，磁盘压力也很小，但是mysql间歇性的性能下跌
* iops 20000 4个1G redo文件,innodb_io_capacity 20000

#### mysql查询很慢
* 等MDL锁
* 等flush
* 等行锁

#### 快速提高性能的办法
* 短连接风暴
* show processlist->id->information_schema 库的 innodb_trx找到空闲连接杀掉
* call query_rewrite.flush_rewrite_rules()让新的规则生效
* force index

#### 主备复制使用row
* 当 binlog_format使用row格式的时候，binlog 里面记录了真实操作行的主键 id，这样 binlog 传到备库去的时候，就肯定会操作 id=4 的行，不会有主备操作不同行的问题。statement格式会导致主备选择不同的索引

#### 循环复制问题
* 通过server_id来解决一致性问题
* stop slave
* change master to ignore_server_ids=(server_id_of_B)

#### 生产数据库设置为双1
* 业务高峰期。一般如果有预知的高峰期，DBA 会有预案，把主库设置成“非双 1”。
* 备库延迟，为了让备库尽快赶上主库。
* 用备份恢复主库的副本，应用 binlog 的过程，这个跟上一种场景类似。
* 批量导入数据的时候。

#### left join
* 如果需要 left join 的语义，就不能把被驱动表的字段放在 where 条件里面做等值判断或不等值判断，必须都写在 on 里面

#### 为什么BNL性能比NLJ性能好
* 在对被驱动表做全表扫描的时候，如果数据没有在 Buffer Pool 中，就需要等待这部分数据从磁盘读入；
* 从磁盘读入数据到内存中，会影响正常业务的 Buffer Pool 命中率，而且这个算法天然会对被驱动表的数据做多次访问，更容易将这些数据页放到 Buffer Pool 的头部
* 即使被驱动表数据都在内存中，每次查找“下一个记录的操作”，都是类似指针操作。而 join_buffer 中是数组，遍历的成本更低。

#### distinct和group by的性能
* 所以不需要执行聚合函数时，distinct 和 group by 这两条语句的语义和执行流程是相同的，因此执行性能也相同。

#### 备库自增主键问题
* 在 insert 语句之前，还有一句 SET INSERT_ID=1。这个线程里下一次需要用到自增值的时候，不论当前表的自增值是多少，固定用 1 这个值。