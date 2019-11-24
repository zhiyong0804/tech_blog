# context.Context

#### 任务取消
* 根 Context:通过 context.Background () 创建
* ⼦子 Context:context.WithCancel(parentContext) 创建
* ctx, cancel := context.WithCancel(context.Background()) 当前 Context 被取消时，基于他的⼦子 context 都会被取消
* 接收取消通知 <-ctx.Done()

```go
func isCancelled(ctx context.Context) bool {
	select {
	case <-ctx.Done():
		return true
	default:
		return false
	}
}

func TestCancel(t *testing.T) {
	ctx, cancel := context.WithCancel(context.Background())
	for i := 0; i < 5; i++ {
		go func(i int, ctx context.Context) {
			for {
				if isCancelled(ctx) {
					break
				}
				time.Sleep(time.Millisecond * 5)
			}
			fmt.Println(i, "Cancelled")
		}(i, ctx)
	}
	cancel()
	time.Sleep(time.Second * 1)
}
```
