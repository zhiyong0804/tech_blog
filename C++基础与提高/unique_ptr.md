# unique_ptr

#### 概述
* 独占式的，专属所有权，同一时刻，只能有一个unique_ptr指针指向这块对象
* 当这个unique_ptr被销毁的时候，它所指向的对象也被销毁

#### make_unique
* c++14才有
* 不支持指定的删除器语法

#### 不支持的操作
* unique_ptr<string> ps1(new string("I love China"));
* unique_ptr<string> ps2(ps1)
* 拷贝，赋值

#### 移动语义
* unique_ptr<int> ps5 = std::move(pi3);

#### release()
* 放弃指针控制权，返回裸指针，将该指针置空
* 裸指针可以释放，可以delete, 也可以初始化另外的智能指针
* 直接release会导致内存泄露
* int* tmp = p5.release();
* delete tmp

#### reset
* 不带参数，释放智能指针所指向的对象，并且将智能指针置为空
* 带参数 释放智能指针所指向的对象，并且将智能指针指向新对象
* pi2.reset(p5.release());

#### nullptr
* 释放智能指针指向的对象，并将智能指针置空

#### 指向数组
* unique_ptr<A> ptr(new A[10]) A没有析构函数，正常
* 有析构函数，会导致异常

#### get()
* 有些函数的参数需要内置的裸指针
* get的指针千万不要delete

#### swap
* 交换智能指针指向的对象

#### 智能指针作为判断条件
* if (ptr)

#### 转换为shared_ptr
* shared_ptr有个显式的构造函数。用于将shared_ptr转换为unique_ptr
* 临时对象都是右值
* unique_ptr没有控制块