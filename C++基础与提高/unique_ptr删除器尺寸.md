# unique_ptr删除器尺寸

#### 临时对象
* unique_ptr不能拷贝，但是在局部临时对象中，即将被销毁的时候，是可以被拷贝的

```
unique_ptr<int> p1;
p1 = func();
```

#### 指定删除器
* unique_ptr<指向的对象,删除器> 智能指针变量名
* 删除器 可调用对象 比如函数 重载

```
void mydelete(int *pdel) {
  delete pdel;
  pdel = nullptr;
}

int main() {
  unique_ptr<int> p1;
  p1 = func();
  typedef void(*fp) (int*);
  using fp = void(*)(int*);
  typedef decltype(mydelete)* fp2;//decltype返回的是void(int*) ,加*表示函数类型
  unique_ptr<int, decltype(mydelete)*> ps2(new int(100), mydelete);
  unique_ptr<int, fp> ps1(new int(100), mydelete);
}
```
* 指定删除器会影响unique_ptr的类型
* unique_ptr删除器不同的智能指针不能放入同一个容器

#### 尺寸问题
* 跟裸指针一样大
* 如果增加了自己的删除器，则尺寸有可能增加，也可能不增加
* 增加删除器会增加unique_ptr尺寸

#### 智能指针设计思想
* 帮助释放内存
* C++ auto_ptr=unique_ptr

#### auto_ptr
* 具有unique_ptr部分特性
* 不能放入容器
* 不能从函数中返回

#### 智能指针
* 多个指向同一一个对象的指针，使用shared_ptr
* 指向同一个对象的指针，使用unique_ptr首选
* 