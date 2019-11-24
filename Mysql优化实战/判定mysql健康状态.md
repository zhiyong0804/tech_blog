# 判定mysql健康状态
#### 并发连接和并发查询
* 在线程进入锁等待以后，并发线程的计数会减一

#### 查表判断

#### 更新判断

#### 内部统计
* performance_schema 库，就在 file_summary_by_event_name 表里统计了每次 IO 请求的时间

#### 检测方案
* update系统表
* 配合检测performace_schema
* 

