# Shared_ptr使用场景，陷阱和性能分析

#### 使用场景
* 临时局部变量返回

```
shared_ptr<int> create(int val) {
  return make_shared<int>(val);
}

shared_ptr<int>func(int val) {
  shared_ptr<int> ptr = create(5);
  return ptr;
}
ptr返回的引用计数没有增加
```
* 不允许隐式转换
* 裸指针绑定到shared_ptr后，管理权限就交给智能指针了，不能再操作裸指针

#### 注意
* 不要delete get()返回的指针
* 不能随便释放get返回的指针

```
  shared_ptr<int> p1(p);
  shared_ptr<int> p2(p); //p2p1无关联，导致p1p2内存被释放两次
  shared_ptr<int> p1(new int);
  shared_ptr<int> p2(p1); //这种写法p1p2互通，使用同一个控制块kkk
```

#### this返回
* 不要把this作为shared_ptr返回
* 裸指针初始化多个智能指针会导致指向同一内存，但是不是同一控制块
* enable_shared_from_this
* shared_from_this
* enable_shared_from_this中又个weak_ptr监视this，shared_from_this,嗲用了lock方法，让引用计数+1，同时返回shared_ptr

#### 性能说明
* shared_ptr weak_ptr是裸指针的两倍
* 第一个指针指向智能指针所指向的对象
* 第二个指针指向的是控制块
* 1）指向对象的强引用计数shared_ptr
* 2) 指向对象的弱引用计数weak_ptr
* 3) 删除器指针 内存分配器
* 控制块是由第一个指向对象的shared_ptr创建的

#### 控制块创建时机
* make_shared 分配并且初始化一个对象，返回对象的shared_ptr

#### 裸指针初始化shared_ptr
* int* p = new  int;
* shared_ptr<int> p1(p)
* shared_ptr<int> p2(p)
* 不允许，会产生多个控制块，就有多个引用计数，导致析构多次

#### 移动语义
* shared_ptr<int> p1(new int(100))
* shared_ptr<int> p2(std::move(p1))
* 移动构造一个新的智能指针对象p2,p1就不再指向该对象，引用计数依旧是一
* shared_ptr<int> p3
* p3 = std::move(p2)
* 移动赋值运算符比拷贝构造函数快

#### 使用建议
* 分配器解决内存分配
* 优先使用make_shared
* shared_ptr<string> s1(new string("new")) 分配两次内存
* auto ps2 = make_shared<string>("new") 分配一次内存 