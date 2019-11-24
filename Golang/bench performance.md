# bench performance
#### tools
* runtime/pprof；
* net/http/pprof；
* runtime/trace；

#### 怎样让程序对 CPU 概要信息进行采样？
* runtime/pprof中的StartCpuProfile和StopCPUProfile函数

#### 怎样设定CPU概要信息的采样频率
* WriteHeapProfile

#### 调试样例
* https://blog.yeeef.com/post/golang-concurrency/
* 

#### 如何为基于 HTTP 协议的网络服务添加性能分析接口？
* import _ "net/http/pprof"
* top -cum
* list main.main
* 