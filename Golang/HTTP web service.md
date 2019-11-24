# HTTP web service
#### http.Client类型中的Transport字段代表着什么？
* http.Transport 是并发安全的
* MaxIdleConns 空闲连接总数
* MaxIdleConnsPerHost Transport值访问的每一个网络服务的最大空闲连接数。
* MaxConnsPerHost 针对某一个Transport值访问的每一个网络服务的最大连接数，不论这些连接是否是空闲的
* ResponseHeaderTimeout：含义是，从客户端把请求完全递交给操作系统到从操作系统那里接收到响应报文头的最大时长。DefaultTransport并没有设定该字段的值。
* ExpectContinueTimeout：含义是，在客户端递交了请求报文头之后，等待接收第一个响应报文头的最长时间

#### ListenAndServe


#### 怎样优雅地停止基于 HTTP 协议的网络服务程序？

```
quit := make(chan os.Signal, 1)                                                                                                                                       signal.Notify(quit, syscall.SIGINT, syscall.SIGTERM, syscall.SIGHUP)                                                                                                  ctx, _ := context.WithTimeout(context.Background(), time.Second*2)
  server := http.Server{
    Addr: "localhost:9099",
  }

  go func() {
    <-quit
    server.Close()
    server.Shutdown(ctx)
  }()

  go server.ListenAndServe()
  select {
  case <-ctx.Done():
    fmt.Println("quit")
  }
```

