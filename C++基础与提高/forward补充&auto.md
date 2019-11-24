# forward补充&auto

#### auto类型常规推断
* auto自动类型推断发生在编译器，不会影响程序性能
* auto定义变量必须初始化
* auto使用灵活
* auto类型推断和函数模版推断非常类似, auto相当于函数模板T

#### 传值方式
```C++
auto xy2 = xy; //xy2=int auto=int传值方式，引用和const类型都被抛弃
```
* 传值的方式引用类型会被抛弃，const属性也会被放弃

#### 指针或者引用
```C++
const auto *xp = &x;//xp = const int*, auto = int;
auto* xp2 = &x; //xp2=int*, auto = int
auto xp3 = &x; //xp3 int*, auto int*
```
* 不会抛弃const限定，但是会丢弃引用

#### 万能引用
```C++
auto&& wn = x;//auto=int&, wn = int&
auto&& wn2 = x2;//auto=int&, wn2 const int &
auto&& wn3 = 100;//auto=int, wn3=int&&
```

#### auto针对数组和函数的推断
```C++
const char mystr[] = "hello"
auto str = mystr //const char*
auto& str2 = str//const char (&)[14]
int a[2] = {1,2};
auto pa = a;//auto=int*, pa=int*
auto tmp = func;//void(*)(double,int)
auto& tmp2 = func;//void(&)(double, int)
tmp(12.3, 4);
tmp2(12.3, 4);
```

#### std::initializer_list
```C++
auto x3 = {30};//auto initializer_list
auto x4{40};
只适合auto，不适合模板
= {} 有隐式类型转换
```

#### auto作为函数返回类型

#### auto不适用类型
* auto不能用于函数参数
* 普通成员变量不能是auto
* 静态成员是可以

```C++
class CT {
 public:
  static const auto i = 5; //ok
  auto j = 5;//err
};
```