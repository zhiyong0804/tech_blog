# mysql修改语句

#### 加锁规则
* 原则 1：加锁的基本单位是 next-key lock
* 原则 2：查找过程中访问到的对象才会加锁。
* 优化1 索引上的等值查询，给唯一索引加锁的时候，next-key lock 退化为行锁。
* 优化2 索引上的等值查询，向右遍历时且最后一个值不满足等值条件的时候，next-key lock 退化为间隙锁。
* bug：唯一索引上的范围查询会访问到不满足条件的第一个值为止。
#### 覆盖索引的注意事项
* select选择的字段中含有不在索引中的字段 ，即索引没有覆盖全部的列。
* where条件中不能含有对索引进行like的操作。

#### 删除数据
* 在删除数据的时候尽量加 limit。这样不仅可以控制删除数据的条数，让操作更安全，还可以减小加锁的范围。

#### 非唯一索引等值锁
* lock in share mode 只锁覆盖索引，但是如果是 for update 就不一样了。 执行 for update 时，系统会认为你接下来要更新数据，因此会顺便给主键索引上满足条件的行加上行锁。
* 如果你要用 lock in share mode 来给行加读锁避免数据被更新的话，就必须得绕过覆盖索引的优化，在查询字段中加入索引中不存在的字段。

#### next-key lock
*  next-key lock具体执行的时候，是要分成间隙锁和行锁两段来执行的

#### 读提交
* 读提交隔离级别下，锁的范围更小，锁的时间更短，这也是不少业务都默认使用读提交隔离级别的原因。
* 