# nginx线程池

#### 线程池
```C
struct thread_pool_s {
    pthread_mutex_t        mtx; //互斥锁
    thread_pool_queue_t   queue; //任务队列
    int_t                 waiting; //等待任务的数量
    pthread_cond_t         cond;

    char                  *name; //线程池名称
    uint_t                threads; //线程池数目
    int_t                 max_queue;
};
```

* pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_DETACHED); 设置线程的分离状态，会释放所有资源
* pthread_join才会释放资源
* 分配任务的时候把上下文的空间一块分配了

```C++
    thread_task_t  *task;

    task = calloc(1,sizeof(thread_task_t) + size);
    if (task == NULL) {
        return NULL;
    }

    task->ctx = task + 1;

    return task;
```
* 退出通过投递任务thread_pool_exit_handler
* 