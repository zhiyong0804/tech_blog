# zookeeper简介
#### 功能
* 中间件 提供协调服务
* 分布式系统，发挥其优势，可以为大数据服务
* 支持java和c语言

#### 分布式系统特性
* 一致性
* 原子性
* 单一视图
* 可靠性 每次对zk的操作状态都保存在服务端
* 实时性 客户端可以读取zk服务端的最新数据

#### zk目录
* docs 文档
* cfg配置
* tickTime 用于计算的时间单元
* initLimit 节点同步到master节点的时间
* synLimit 心跳时间

#### 基本模型
* 树形结构来区分目录
* 每一个节点为znode
* 临时节点和永久节点
* zk节点都有各自的版本号
* 节点数据发生变化，节点的版本号会累加(乐观锁)
* 删除修改过时节点，版本号不匹配会报错
* zk节点存储的数据不适宜过大，几K
* 节点可以设置权限acl，控制用户的访问

#### zookeeper作用
* master节点选举
* 统一配置文件管理，比如修改了redis统一配置
* 分布与订阅，发布者把数据存在znode上，订阅者会读取这个数据
* 分布式锁，分布式环境中不同进程之间争夺资源，类似多线程的锁
* 集群管理，保证数据的强一致性

#### 常用命令行
* ls ls2
* ls2 查看状态信息

```
cZxid = 0x0 创建的id
ctime = Thu Jan 01 08:00:00 CST 1970
mZxid = 0x0
mtime = Thu Jan 01 08:00:00 CST 1970
pZxid = 0x50000002f 子节点的id
cversion = 18 子节点
dataVersion = 0 当前节点的数据版本号
aclVersion = 0 
ephemeralOwner = 0x0
dataLength = 0
numChildren = 12
```

#### session基本原理
* 客户端和服务器之间的连接存在会话
* 每个会话都可以设置一个超时时间
* 心跳结束，session则过期
* session过期，临时节点znode全部会被抛弃
* 心跳机制 客户端向服务端的ping包

#### 常用命令
* create -e path data
* 临时节点 ephemeralOwner = 0x1000a643cae0002，临时节点会在时效性结束后被删除
* 持久节点 ephemeralOwner = 0x0
* 顺序节点 create -s /imooc/sec seq
* set path data 乐观锁

#### watch机制
* 针对每个节点的操作，都会有一个监督者，watcher
* 当监控的某个对象znode发生了变化，则触发watcher事件
* zk的watcher是一次性的，触发后立即销毁
* 父子节点增删改都能够出发其watcher
* watcher事件也不同
* 子节点创建事件
* 子节点删除事件
* 子节点数据变化事件

#### 父节点事件
* get 
* NodeCreate
* NodeDataChanged
* NodeDeleted

#### 子节点事件
* ls 创建子节点 NodeChildrenChanged
* ls 删除子节点 NodeChildrenChanged
* ls 修改子节点不触发事件 必须set -s /imooc/xyz 9090

#### wathcher资源配置
* 统一资源配置

### ACL权限控制
* getAcl 
* setAcl
* addauth 输入认证授权信息
* Zk 的 ac 通过【scheme: id: permissions】来构成权限列表 scheme：代表采用的某种权限机制 d：代表允许访问的用户 permissions：权限组合字符串
* world 
* auth 认证登录
* digest需要对密码加密才能访问

#### permissions
* crdwa
* create
* read
* write
* delete
* admin 设置权限

#### digest
* addauth digest imooc:imooc
* setAcl /names/imooc  auth:imooc:imooc:adrwa
* 设置 setAcl -s /names/test digest:imooc:XwEDaL3J0JQGkRQzM0DpO6zMzZs=:cdra
* 登录 addauth digest imooc:imooc
* 获取数据 get /names/test

#### ip
* setAcl /names/ip ip:192.168.31.224:cdrwa
* getAcl -s /names/ip

#### acl常用场景
* 开发和测试环境分离
* 生产环境可以控制指定ip的服务可以访问相关节点，防止混乱

#### zk四字命令
* echo stat | nc master 2181
* echo ruok | nc master 2181
* echo dump | nc master 2181 临时节点
* cons 连接信息
* mntr 健康信息
* wchs watch信息
* wchc与wchp session 与 watch 及 path 与 watch 信息