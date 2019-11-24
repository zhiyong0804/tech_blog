# lambda

#### 特点
* 是个匿名函数, 可调用的代码单元
* 返回类型可以自动推导，可以省略
* 没有参数的时候()可以省略·

```C++
auto f = [](int a)->int {
    return a + 1;
  };

  auto f1 = [](int a) {
    return a + 1;
  };

  auto f2 = [] {return  2;};
```

#### 捕获列表
* 捕获一定范围内的变量
* lambda可以捕获局部静态变量

```C++
  static int i = 9;
  auto f3 = [] {
    return i;
  };

```
* [&] 捕获所有外部变量

```C++
  int j = 9;
  auto f4 = [&] {
    j = 10;
  };
  f4();
```
* [=] 值使用，只能用，不能修改

```C++
auto f5 = [=] {
    cout << "j " << j << endl;
  };
  f5();

```
* [this] 用于类中，拥有和成员函数一样的权限
* [变量名] 表示按值捕获，同时不捕获其他变量，按值分隔

```C++
void func(int x, int y) {
    //auto ld = [this] {
    //auto ld = [this, x, y] {
    auto ld = [this, &x, &y] {
      x = 5;
      y = 2;
      return m_i;
    };
  }
```
* [=,&变量名] 按值捕获所有变量, 但按引用捕获&中所指的变量，第一个都是默认捕获，隐式捕获方式，其他的是显式捕获，后面的引用方式是相反的[&,x]

```C++
 int x = 2, y = 3;
  auto f6 = [=, &x, &y] {
    cout << "j " << j << endl;
  };
  f6();

  auto f7 = [&, x, y] {
    cout << "j " << j << endl;
  };
  f6();
```

#### lambda表达式延迟调用易出错
```C++
  int x1 = 5;
  auto f8 = [=] {
    return x1;
  };
  x1 = 10;
  cout << f8() << endl;//5
```
* lamda表达式引用外部变量的那一刻，复制了一份存储在表达式中
* 如果采用引用，就不会出错

```C++
 int x1 = 5;
  auto f8 = [&] {
    return x1;
  };
  x1 = 10;
  cout << f8() << endl;//5
```

#### mutable
```C++
  int x2 = 5;
  auto f9 = [=]() mutable {
    x2 = 7;
    return x2;
  };
  cout << f9() << endl;
```
* 可以修改按值捕获的变量

#### 表达式类型和存储
* Closure Type
* 闭包 函数内的函数，本质就是lambda表达式创建时运行时间对象
* 闭包类的类型的对象，可以认为是带有operator()的类型的对象

```C++
int x = 5;
auto f = [&] {
  return x;
}

function<int(int)> fc1 = [](int t) {return  t;};
  cout << fc1(15) << endl;
function<int(int)> fc2 = bind(
      [](int t) {
      return t;
      },
      16
      );
```

#### 总结
```C++
  vector<int> vec = {10,20,30,40,50};
  int sum = 0;
  for_each (vec.begin(), vec.end(), [&sum](int val) {
            sum += val;
            cout << val << endl;
            });
  cout << " sum " << sum << endl;

  find_if (vec.begin(), vec.end(), [](int val) {
           cout << val << endl;
           if (val > 15)
              return true;
           return false;
           });
```