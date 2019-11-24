# Hash索引和索引使用原则

#### Hash区别
* Hash 索引不能进行范围查询，而 B+ 树可以。这是因为 Hash 索引指向的数据是无序的，而 B+ 树的叶子节点是个有序的链表。
* Hash 索引不支持联合索引的最左侧原则
* Hash 索引不支持 ORDER BY 排序和模糊搜索

#### 索引使用原则
* 字段有唯一性的限制
* 频繁使用where查询
* 需要使用group by 和 order by

```
SELECT user_id, count(*) as num FROM product_comment group by user_id order by comment_time desc limit 100
```
* UPDATE、DELETE 的 WHERE 条件列
* DISTINCT 字段需要创建索引 索引会对数据按照某种顺序进行排序，所以在去重的时候也会快很多
* 多表 JOIN 连接操作时, 表不要超过3张

#### 不实用索引
* WHERE 条件（包括 GROUP BY、ORDER BY）里用不到的字段不需要创建索引
* 表记录太少
* 字段中有大量重复数据
* 频繁更新的字段不一定要创建索引

#### 索引失效
* 如果索引进行了表达式计算，则会失效
* 如果对索引使用函数，也会造成失效
* WHERE 子句中，如果在 OR 前的条件列进行了索引，而在 OR 后的条件列没有进行索引，那么索引会失效
* 当我们使用 LIKE 进行模糊查询的时候，前面不能是 %
* 索引列尽量设置为 NOT NULL 约束
* 我们在使用联合索引的时候要注意最左原则， 一条 SQL 语句可以只使用联合索引的一部分，但是需要从最左侧开始，否则就会失效，联合索引如果语句都使用的话就不会失效
* 