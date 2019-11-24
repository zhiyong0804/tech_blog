# recursive_mutex

#### 概念
* 独占，可以递归lock，允许同一个互斥量多次被lock
* 允许同一个线程，同一个互斥量多次被调用，效率比mutex低一些

```
std::recursive_mutex mutex1;
std::lock_guard<std::recursive_mutex> s1(mutex1);
std::lock_guard<std::recursive_mutex> s2(mutex1);
//可以多次调用
```

#### std::timed_mutex&std::recursive_timed_mutex
* try_lock_for  等待一段时间，如果规定时间内没有拿到锁，就直接走下去

```
 for (int i = 0; i < 10000; i++) {
      //std::lock_guard<std::timed_mutex> s1(mutex1);
      std::chrono::milliseconds timeout(100);
      //mutex1.try_lock_until(chrono::steady_clock::now() + timeout)
      if (mutex1.try_lock_for(timeout)) {
        cout << "get lock" << endl;
        msgRecvQueue.push_back(i);
        cout << "insert ele " << i << endl;
        mutex1.unlock();
      } else {
        cout << "not get lock sleep " << endl;
        std::this_thread::sleep_for(timeout);
      }
    }
```
* 

