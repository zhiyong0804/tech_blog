# HTTP缓存控制
#### 服务器控制
* no_store：不允许缓存，用于某些变化非常频繁的数据，例如秒杀页面；
* no_cache：它的字面含义容易与 no_store 搞混，实际的意思并不是不允许缓存，而是可以缓存，但在使用之前必须要去服务器验证是否过期，是否有最新的版本；
* must-revalidate：又是一个和 no_cache 相似的词，它的意思是如果缓存不过期就可以继续使用，但过期了如果还想用就必须去服务器验证。
* ![8a67535620ab9c7764560363f83982b2](media/15636770588929/8a67535620ab9c7764560363f83982b2.png)

#### cache-control
* 客户端和服务端都有

#### 控制缓存
* client if-Modified-Since和If-None-Match
* server Last-modified和ETag
* Last-modified最后的修改时间
* ETag是实体标签（Entity Tag）的缩写，是资源的一个唯一标识，主要是用来解决修改时间无法准确区分文件变化的问题
* 强 ETag 要求资源在字节级别必须完全相符，弱 ETag 在值前有个“W/”标记，只要求资源在语义上没有变化，但内部可能会有部分发生了改变

#### 注意
* no_cache等同于max-age=0,must-revalidate
* 如果只有last-modified，没有Cache-Control和Expires，使用启发算法计算缓存时间
* nginx的etag计算是修改时间+长度