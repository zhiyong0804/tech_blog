# loadbalance algorithm

#### 负载均衡
* 负载均衡算法

#### 自动输出对象
```
func (p *Instance) String() string {
  return p.host + ":" + strconv.Itoa(p.port)
}
```

#### 一致性哈希算法
* 一致性哈希 //TODO