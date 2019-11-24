# Sql语句执行流程
#### 基础架构示意图
![0d2070e8f84c4801adbfa03bda1f98d9](media/15429421228977/0d2070e8f84c4801adbfa03bda1f98d9.png)

#### 查看sql连接
* show processlist

#### 长连接
* 定期断开长连接
* 定期执行mysql_reset_connection

#### 语法错误
* 关注user near

#### 查询缓存
* query_cache_type
* 
