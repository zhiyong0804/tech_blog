# Slub分配器
#### kmem_cache_alloc_node
* 创建 task_struct 的时候，我们不用到内存里面去分配，先在缓存里面看看有没有直接可用的，这就是kmem_cache_alloc_node的作用
* 进程结束，task_struct 也不用直接被销毁，而是放回到缓存中，这就是kmem_cache_free的作用

#### kmem_cache
```
struct kmem_cache {
   struct kmem_cache_cpu __percpu *cpu_slab;
   struct kmem_cache_node *node[MAX_NUMNODES];
}
```

#### kmem_cache_cpu
* 分配缓存的快速通道
* 

#### kmem_cache_node
* 分配缓存的普通通道

#### 内存分配
* https://www.cnblogs.com/luoahong/p/10907734.html
* 