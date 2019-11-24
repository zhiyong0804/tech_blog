# search机制
#### 搜索返回结果
* took：整个搜索请求花费了多少毫秒
* hits.total：本次搜索，返回了几条结果
* hits.max_score：本次搜索的所有结果中，最大的相关度分数是多少，每一条document对于search的相关度，越相关，_score分数越大，排位越靠前
* hits.hits：默认查询前10条数据，完整数据，_score降序排序
* shards：shards fail的条件（primary和replica全部挂掉），不影响其他shard。默认情况下来说，一个搜索请求，会打到一个index的所有primary shard上去，当然了，每个primary shard都可能会有一个或多个replic shard，所以请求也可以到primary shard的其中一个replica shard上去。
* timeout：默认无timeout，latency平衡completeness，手动指定timeout，timeout查询执行机制

#### timeout机制
* Timeout 机制，指定每个 shard，就只能在 timeout 时间范目内，将搜索到的部分数据（也可能全都搜素到了），直接理解返回给 client 程序，而不是等到所有的数据全都推素出来以后再返回
* 确保说，一次搜索请求可以在用户指定的 timeout 时长内完成。为一些时间敏感的搜素应用提供良好的支持。
* 