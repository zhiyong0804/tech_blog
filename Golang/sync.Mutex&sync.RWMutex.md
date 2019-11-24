# sync.Mutex&sync.RWMutex
#### mutex attention
* 不要重复锁定
* 不要忘记解锁互斥锁，必要时使用defer
* 不要对尚未锁定或者已经解锁的互斥锁解锁
* 不要在多个函数之间传递互斥锁
* 注意 对于每一个锁定操作，都要有且只有一个对应的解锁操作
* mutex是值类型，函数传递或者进入通道都会导致副本产生
* 不要试图解锁未锁定的锁，会引发panic

#### question
* 互斥锁和读写锁的指针类型实现了哪一个接口
* Locker接口
* 怎样获取读写锁中的读锁
* RLock
* race 查看程序中是否有竞争关系

#### code
```
  mu  sync.RWMutex
  mu.Lock()
  defer mu.RUnlock()
  
```

#### add line numbers to log Output
```
   log.SetFlags(log.LstdFlags | log.Lshortfile)
```

