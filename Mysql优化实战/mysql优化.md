# mysql优化
#### 短连接风暴
* 提高max_connections
* 跳过权限认证阶段

#### 慢查询
* 索引没有设计好 主备切换，
* sql语句没写好 query_rewrite

```
  insert into query_rewrite.rewrite_rules(pattern, replacement, pattern_database) values ("select * from t where id + 1 = ?", "select * from t where id = ? - 1", "db1");
```
* mysql选错了索引

#### QPS暴增问题
* 去掉白名单
* 删除用户
* 