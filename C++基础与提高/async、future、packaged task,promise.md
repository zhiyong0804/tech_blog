# async、future、packaged task,promise

#### async
* std::async是一个函数模版。启动一个异步任务, 返回一个future对象
* 自动启动一个线程并开始执行对应的线程入口函数，future对象里就含有线程入口函数，可以通过调用future对象的成员函数
* future提供了一种访问异步操作结果的机制，就是说结果可能没有办法马上拿到，在线程执行完毕的时候，就能够拿到结果了

```C++
std::future<int> result = std::async(mythread);
cout << "continue...!" << endl;
cout << result.get() << endl;
```
* get()函数等待线程执行结束后返回结果
* get只能获取一次
* 如果没有get和wait依然等待线程执行结束后获取结果

##### std::launch
* std::launch:deferred 表示线程入口函数调用被延迟到std::future的wait或者get函数调用才执行

```
std::future<int> result = std::async(std::launch::deferred, &A::mythread, &a, val);
```
* 如果下面不调用get或者wait，线程不会被创建

##### std::launch::async
```
std::future<int> result = std::async(std::launch::deferred, &A::mythread, &a, val);
```
* std::launch::async不能和std::launch:deferred同时使用

#### std::packaged_task 
* 打包任务，是一个类模板，模板参数是各种调用对象

```C++
std::packaged_task<int(int)> mypt(mythread2);
std::thread t1(std::ref(mypt), 1);
t1.join();
std::future<int> res = mypt.get_future();
```
* lamda

```
std::packaged_task<int(int)> mypt([](int mypar) {
                                    cout << "thread start " << " thread_id = "
                                    << std::this_thread::get_id() << endl;
                                    cout << " val " << mypar << endl;
                                    std::chrono::seconds dura(1);
                                    std::this_thread::sleep_for(dura);
                                    return 5;
                                    });
mypt(1);                                    
```

#### std::promise
* 通过promise保存一个值，然后在其他线程中，把这个值取出来用

```
std::promise<int> myprom;
std::thread t1(mythread3, std::ref(myprom), 180);
t1.join();
std::future<int> ful = myprom.get_future();
auto res = ful.get();
```
* 传递给另外的线程

```
void mythread4(std::future<int>& tmp) {
  auto res = tmp.get();
  cout << "mythread4 res " << res << endl;
}
std::promise<int> myprom;
std::thread t1(mythread3, std::ref(myprom), 180);
t1.join();
std::future<int> ful = myprom.get_future();
std::thread t2(mythread4, std::ref(ful));
```

#### 小结
* 我们学习这些东西的目的，并不是要把他们都用在咱们自己的实际开发中。
* 相反，如果我们能够用最少的东西能够写出一个稳定、高效的多线程程序，更值得赞赏
* 我们为了成长，必须要阅读一些高手些的代码，从而快速实现自己代码的积累，我们的技术就会有一个大幅度的提升 //老师更愿意将学习这些内容的理由解释为：为我们将来能够读懂高手甚至大师些的代码而铺路；