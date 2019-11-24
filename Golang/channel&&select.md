# channel&&select

#### 特性
* 唯一一个可以满足并发安全性的类型
* 发送操作互斥，接收操作也是
* 有类型

#### 特点
* 元素从外界进入通道是副本
* 发送包括复制元素值，放置副本到通道内部

#### 通道阻塞
* 满时写
* 空时收
* nil 读写会永久阻塞

#### panic
* 关闭的通道发送操作会引起，接收操作不会引起，仍然可以读取数据
* 通道是浅拷贝
* 通道底层数据是环形链表
* 通道的长度表示当前的元素个数，容量是初始化的

####  单向通道
* 函数调用方的约束

#### select语句的分支选择规则
* 从左到右执行
* 分支case表达式从上到下执行
* case语句操作正在阻塞的话，跳过该分支
* 所有候选语句的表达式都执行完毕后，才会挑选分支
* 如果select语句发现同时有多个候选分支满足选择条件，那么它就会用一种伪随机的算法在这些分支中选择一个并执行。注意，即使select语句是在被唤醒时发现的这种情况，也会这样做。

##### select并发执行
* goto 

```
func BreakForSelect2() {
  intChan := make(chan int, 1)
  intChan2 := make(chan int, 1)
  intChan3 := make(chan int, 1)
  countChan := 0
  go SetValue(intChan, 1)
  go SetValue(intChan2, 2)
  go SetValue(intChan3, 3)

loop:
  for {
    select {
    case val, ok := <-intChan:
      if ok {
        fmt.Println("val ", val)
        countChan++
        close(intChan)
        intChan = nil
      }

    case val, ok := <-intChan2:
      if ok {
        fmt.Println("val ", val)
        countChan++
        close(intChan2)
        intChan2 = nil
      }

    case val, ok := <-intChan3:
      if ok {
        fmt.Println("val ", val)
        countChan++
        close(intChan3)
        intChan3 = nil
      }

    default:
      fmt.Println("dealult")
      if countChan == 3 {
          break loop
          //goto end
      }
      time.Sleep(1 * time.Second)
    }
  }
  //end:
  fmt.Println("End")
}
```

* 跳过分支

```
  func SelectSkip() {
  intChan := make(chan int, 1)
  intChan2 := make(chan int, 1)
  intChan3 := make(chan int, 1)
  intChan2 <- 2
  close(intChan)
  for {
    select {
    case val, ok := <-intChan:
      if ok {
        fmt.Println("val ", val)
      } else {
        fmt.Println("change chan to nil")
        intChan = nil
      }
    case val, ok := <-intChan2:
      if ok {
        fmt.Println("val", val)
      } else {
        fmt.Println("no val")
      }
    case <-intChan3:
      fmt.Println("case 3")
      intChan3 = nil
    default:
      fmt.Println("continue")
    }
    time.Sleep(1 * time.Second)
  }
}
```

#### 监测管道关闭
```
  for {
    b, ok := <-ch
    if ok == false {
      fmt.Println("channel is closed")
      break
    }
    fmt.Println(b)
  }
  或者直接关闭channel
```
* 向关闭的 channel 发送数据，会导致 panic
* v, ok <-ch; ok 为 bool 值，true 表示正常接受，false 表示通道关闭
* 所有的 channel 接收者都会在 channel 关闭时，⽴立刻从阻塞等待中返回且上 述 ok 值为 false。这个⼴广播机制常被利利⽤用，进⾏行行向多个订阅者同时发送信号。 如:退出信号。

#### 多任务并发执行等待完全结束
```
func test1(boolChan chan bool) {
  time.Sleep(time.Second * 1)
  fmt.Println("test1")
  boolChan <- true
}

func test2(boolChan chan bool) {
  time.Sleep(time.Second * 2)
  fmt.Println("test2")
  boolChan <- true
}

func test3(boolChan chan bool) {
  time.Sleep(time.Second * 3)
  fmt.Println("test3")
  boolChan <- true
}

func main() {
  boolChan := make(chan bool, 3)

  go test1(boolChan)
  go test2(boolChan)
  go test3(boolChan)
  for i := 0; i < 3; i++ {
    <-boolChan
  }
}
```

#### routine之间通讯同步
```
  func send(ch chan int, exitChan chan struct{}) {
  for i := 0; i < 10; i++ {
    ch <- i
  }

  close(ch)

  var a struct{}
  //exitChan <- true
  exitChan <- a
}

func recv(ch chan int, exitChan chan struct{}) {
  for {
    v, ok := <-ch
    if !ok {
      break
    }
    fmt.Println(v)
  }

  var a struct{}
  exitChan <- a
  //exitChan <- true
}

func main() {
  ch := make(chan int, 10)
  exitChan := make(chan struct{}, 2)
  go send(ch, exitChan)
  go recv(ch, exitChan)

  for i := 0; i < 2; i++ {
    <-exitChan
  }
}
```

#### 超时控制
```go
func TestSelect(t *testing.T) {
	select {
	case ret := <-AsyncService():
		t.Log(ret)
	case <-time.After(time.Millisecond * 100):
		t.Error("time out")
	}
}
```

#### select 控制
```go
func isCancelled(cancelChan chan struct{}) bool {
	select {
	case <-cancelChan:
		return true
	default:
		return false
	}
}
```
