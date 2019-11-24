#  std::atomic

#### std::atomic
* 一般atomic原子操作，支持++,--,-=,+=其他的不支持

#### std::async
* async一般叫创建异步任务
* async有时候并不创建线程
* deferred延迟调用，并且不创建新线程，延迟到future对象调用get或者wait的时候才执行线程函数，如果没有调用get或者wait，那么mythread不会执行

##### std::launch::async
```
std::future<int> res = std::async(std::launch::async, mythread);
```
* 强制异步任务在新线程上执行

##### std::launch::async|std::launch::deferred
```
std::future<int> res = std::async(std::launch::async | std::launch::deferred, mythread);
```
* 可能是创建新线程立即执行
* 也可能是没有创建新线程，并且延迟到res.get()才开始执行任务入口函数

##### 不带额外参数，只给async函数一入口函数名
```
std::future<int> res = std::async(std::launch::async | std::launch::deferred, mythread);
```
* 默认值是std::launch::async|std::launch::deferred

##### 系统如何自行决定
* std::async和std::thread
* std::thread创建线程，如果系统资源紧张，创建线程异常，整个程序就会报异常错误
* std::async创建异步任务，可能创建，也可能不创建，容易拿到入口函数的返回值
* async创建失败不会引起系统崩溃，可能执行在主线程上，不创建新的线程
* 如果一定要创建std::launched::async, 可能是程序崩溃
* 默认写法判定系统是否创建线程需要wait_for

```
std::future_status status = result.wait_for(std::chrono::seconds(0));
if (status == std::future_status::deferred) {
  //delay exec 
}
```
