# Mysql的count(*)
#### 问题
* MyISAM 表虽然 count(*) 很快，但是不支持事务；
* show table status 命令虽然返回很快，但是不准确；
* InnoDB 表直接 count(*) 会遍历全表，虽然结果准确，但会导致性能问题。

#### 用一个表存储记录数目
* 在事物操作中修改这个表
* 先增加表的计数
* commit时再插入数据
* 

#### 各种count差别
* count(*)
* count(primary key)
* count(字段)
* count(1)
* count(主键 id) 来说，InnoDB 引擎会遍历整张表，把每一行的 id 值都取出来，返回给 server 层。server 层拿到 id 后，判断是不可能为空的，就按行累加。
* 对于 count(1) 来说，InnoDB 引擎遍历整张表，但不取值。server 层对于返回的每一行，放一个数字“1”进去，判断是不可能为空的，按行累加
* count(字段) 如果这个“字段”是定义为 not null 的话，一行行地从记录里面读出这个字段，判断不能为 null，按行累加；如果这个“字段”定义允许为 null，那么执行的时候，判断到有可能是 null，还要把值取出来再判断一下，不是 null 才累加。