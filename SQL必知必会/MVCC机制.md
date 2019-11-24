# MVCC机制

#### 解决幻读
* MVCC+Next-Key Lock

#### MVCC解决问题
* 读写之间阻塞的问题，通过 MVCC 可以让读写互相不阻塞，即读不阻塞写，写不阻塞读，这样就可以提升事务并发处理能力。
* 降低了死锁的概率。这是因为 MVCC 采用了乐观锁的方式，读取数据时并不需要加锁，对于写操作，也只锁定必要的行。
* 解决一致性读的问题

#### MVCC实现
* 事务版本号
* 行记录中隐藏列 db_row_id db_trx_id db_roll_ptr
* Undo Log
* 表空间中包含多个段，数据段、索引段、回滚段

#### ReadView工作机制
* trx_ids，系统当前正在活跃的事务 ID 集合。
* low_limit_id，活跃的事务中最大的事务 ID。
* up_limit_id，活跃的事务中最小的事务 ID。
* creator_trx_id，创建这个 Read View 的事务 ID。
* trx_id < up_limit_id 可见
* trx_id > low_limit_id 不可见
*  up_limit_id < trx_id < low_limit_id 如果trx_id in trx_ids 去回滚段中找
*  trx_id not in trx_ids 事务已经提交 

#### 读提交和可重复读的区别
* “读已提交”时，每次SELECT操作都创建Read View，无论SELECT是否相同，所以可能出现前后两次读到的结果不等，即不可重复读。
* “可重复读”时，首次SELECT操作才创建Read View并复用给后续的相同SELECT操作，前后两次读到的结果一定相等，避免了不可重复读。