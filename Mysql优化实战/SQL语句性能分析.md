# SQL语句性能分析
#### 条件字段函数分析
* 索引字段做函数操作，可能会破坏索引值的有序性，因此优化器就决定放弃走树搜索功能。
* 索引字段的参数加上函数操作可以使用索引

#### 隐性类型转换
```
  mysql> select * from tradelog where tradeid=110717;
```
* 数据类型转换规则会触发函数字段操作会放弃索引树

#### 隐式字符编码转换
```
 mysql> CREATE TABLE `tradelog` (
  `id` int(11) NOT NULL,
  `tradeid` varchar(32) DEFAULT NULL,
  `operator` int(11) DEFAULT NULL,
  `t_modified` time DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `tradeid` (`tradeid`),
  KEY `t_modified` (`t_modified`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
 CREATE TABLE `trade_detail` (
  `id` int(11) NOT NULL,
  `tradeid` varchar(32) DEFAULT NULL,
  `trade_step` int(11) DEFAULT NULL, /* 操作步骤 */
  `step_info` varchar(32) DEFAULT NULL, /* 步骤信息 */
  PRIMARY KEY (`id`),
  KEY `tradeid` (`tradeid`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```
* 主动把被驱动表的字符改成utf8
```
select d.* from tradelog l, trade_detail d where d.tradeid=l.tradeid and l.id=2; 
select d.* from tradelog l , trade_detail d where d.tradeid=CONVERT(l.tradeid USING utf8) and l.id=2; 
```
