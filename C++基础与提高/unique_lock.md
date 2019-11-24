# unique_lock

#### 用法
* unique_lock类模版, 一般lock_guard
* 灵活，但是效率差一点，内存多占一点

#### 参数
* std::adopt_lock 表示互斥量已经被lock, 线程已经拥有了锁的所有权

#### try_to_lock
* 执行前提不能锁住mutex
* try_to_lock去锁定mutex，如果没有锁定mutex，会立即返回，并不会阻塞

```
std::unique_lock<std::mutex> s1(mutex1, std::try_to_lock);
if (!s1.owns_lock()) {
  cout << " do else thing "  << endl;
  continue;
}
```

#### defer_lock
* 不能自己先lock，否则会报异常
* 并没有给mutex加锁，初始化了一个没有加锁的mutex

#### unique_lock成员函数
```
std::unique_lock<std::mutex> s1(mutex1, std::defer_lock);
s1.lock()
//执行非共享代码
s1.unlock()
```
* 引入s1.unlock()想灵活处理一些非共享代码
* try_lock 尝试给互斥量加锁，如果不成功，不阻塞

```
if (!s1.try_lock()) {cout << " do else thing " << endl; continue;}
```
* release() 返回管理的mutex指针，并释放所有权，unique_lock和mutex不再关联

```
std::unique_lock<std::mutex> s1(mutex1);
mutex* ptr = s1.release();
//业务代码
ptr.unlock
```
* 如果原来mutex处于加锁状态，需要是按那个，有时候unlock是需要灵活控制代码粒度
* unique_lock所有权可以转移给其他的unique_lock,不能复制

```
//1 std::move
std::unique_lock<std::mutex> s1(mutex1);
std::unique_lock<std::mutex> s2(s1);
//2 function return
std::unique_lock<mutex> ret_lock () {
  std::unique_lock<mutex> tmp(mutex1);
  return tmp;
}
函数返回一个局部的unique_lock对象是可以的，会让系统临时生成
unique_lock移动构造函数
```
* 