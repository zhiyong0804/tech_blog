# shared_ptr常用操作

#### 引用计数增加
* 每个shared_ptr都会记录其他的shared_ptr指向对象
* 智能当成实参在函数里传递会增加
* 如果是引用则不增加
* 返回值返回

```
void func(shared_ptr<int>& ptr);不增加
void func(shared_ptr<int> ptr);//增加
shared_ptr<int> func2(shared_ptr<int>& ptr)//增加
```

#### 引用计数减少
* 局部的shared_ptr离开其作用域
* 指向其他对象

#### 常用操作
* use_count
* unique

#### reset  
* 若pi是唯一指向该对象的指针，那么释放pi指向的对象，pi置空，否则引用计数减q，不释放对象，同时pi置空
* reset带参数的话
* 空指针也可以用reset，指向新的对象

#### get
* 返回裸指针
* 如果智能指针释放了对象，裸指针也就无效了
* 第三方函数就是需要裸指针

#### swap
* 交换两个智能指针的对象

#### nullptr
* 将指向的对象引用计数减去1，若为0，释放智能指针指向的对象

#### 指定删除器和数组问题
* 默认将delete作为资源析构方式
* 我们可以指定自己的删除器来取代系统提供的默认的删除器

```
void  myDelete(int* p) {
  cout << __func__ << endl;
  delete p;
}
shared_ptr<int> p11(new int(12345), myDelete);
  shared_ptr<int> p12(p11);
  p12.reset();
  p11.reset();
```
* 系统默认删除器处理不了数组

```
* shared_ptr<A> pA(new A[10]);//系统释放PA是delete pA所指向的裸指针，而不delete[]pA
* defalut_delete
* shared_ptr<A> pB(new A[10], default_delete<A[]>());
* shared_ptr<A[]> pC(new A[10]);
```

#### 指定删除器额外说明
* shared_ptr指定的删除器不同，但是指向的类型的相同
* 可以放入同一个容器