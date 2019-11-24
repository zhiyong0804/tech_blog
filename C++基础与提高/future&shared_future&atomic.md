# future&shared_future&atomic

#### future
```
  std::future<int> result = std::async(std::launch::deferred, mythread);
  cout << "continue...!" << endl;

  std::future_status status = result.wait_for(std::chrono::seconds(4));
  if (status == std::future_status::timeout) {
    cout << "timeout func thread not finished " << endl;
  } else if (status == std::future_status::ready) {
    cout << "thread suc finished " << endl;
  } else if (status == std::future_status::deferred) {
    //如果async第一个条件std::launch::deferred
    cout << "thread deferred " << endl;
    cout << result.get() << endl;
  }
```

#### shared_future
```
std::packaged_task<int(int)> mypt(mythread2);
std::thread t1(std::ref(mypt), 1);
t1.join();
std::future<int> res = mypt.get_future();
std::shared_future<int> result_s(std::move(res));

bool valid = res.valid();//判断future对象值是否是有效值
std::shared_future<int> result_s(res.share());

valid = res.valid();
auto getRes = result_s.get();
getRes = result_s.get();



std::shared_future<int> result_s(mypt.get_future());
std::thread t2(mythread4, std::ref(result_s));
t2.join();
cout << " I love China" << endl;
```

#### std::atomic
* 