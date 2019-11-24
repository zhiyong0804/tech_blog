# sync.WaitGroup&sync.Once
#### sync.Once类型值的Do方法是怎么保证只执行参数函数一次的？
* atomic.LoadUint32函数来获取该字段的值，并且一旦发现该值为1就会直接返回
* 通过单例模式来实现

```
func (o *Once) Do(f func()) {
  if atomic.LoadUint32(&o.done) == 1 {
    return
  }
  // Slow-path.
  o.m.Lock()
  defer o.m.Unlock()
  if o.done == 0 {
    defer atomic.StoreUint32(&o.done, 1)
    f()
  }
}
```

#### Do的特点
* 第一个特点，由于Do方法只会在参数函数执行结束之后把done字段的值变为1，因此，如果参数函数的执行需要很长时间或者根本就不会结束（比如执行一些守护任务），那么就有可能会导致相关 goroutine 的同时阻塞。
* 第二个特点，Do方法在参数函数执行结束后，对done字段的赋值用的是原子操作，并且，这一操作是被挂在defer语句中的。因此，不论参数函数的执行会以怎样的方式结束，done字段的值都会变为1。

#### 案例
```go
type Singleton struct {
	data string
}

var singleInstance *Singleton
var once sync.Once

func GetSingletonObj() *Singleton {
	once.Do(func() {
		fmt.Println("Create Obj")
		singleInstance = new(Singleton)
	})
	return singleInstance
}

func TestGetSingletonObj(t *testing.T) {
	var wg sync.WaitGroup
	for i := 0; i < 10; i++ {
		wg.Add(1)
		go func() {
			obj := GetSingletonObj()
			fmt.Printf("%X\n", unsafe.Pointer(obj))
			wg.Done()
		}()
	}
	wg.Wait()
}

```