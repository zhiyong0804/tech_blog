# Join语句优化
#### Multi-Range Read
* 根据索引 a，定位到满足条件的记录，将 id 值放入 read_rnd_buffer 中 ;
* 将 read_rnd_buffer 中的 id 进行递增排序；
* 排序后的 id 数组，依次到主键 id 索引中查记录，并作为结果返回
* read_rnd_buffer由read_rnd_buffer_length
* 优化核心原因
* MRR 能够提升性能的核心在于，这条查询语句在索引 a 上做的是一个范围查询（也就是说，这是一个多值查询），可以得到足够多的主键 id。这样通过排序以后，再去主键索引查数据，才能体现出“顺序性”的优势。

#### Batched Key Access
* NLJ的优化一次性驱动表多取出索引值跟被驱动表进行多行匹配
* BNL优化把
* 表 t2 中满足条件的数据放在临时表 tmp_t 中；
* 给临时表 tmp_t 的字段 b 加上索引；
* 让表 t1 和 tmp_t 做 join 操作。

#### SQL操练语句
```
explain select * from t2 where a>=100 and a<=200;
explain select * from t1 join t2 on (t1.b=t2.b) where t2.b>=1 and t2.b<=2000;
优化BNL
create temporary table temp_t(id int primary key, a int, b int, index(b))engine=innodb;
insert into temp_t select * from t2 where b>=1 and b<=2000;
select * from t1 join temp_t on (t1.b=temp_t.b);
```