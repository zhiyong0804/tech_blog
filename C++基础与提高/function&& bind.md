# function&& bind

#### 可调用对象
* 函数
* 重载()对象

#### 可被转换的函数指针的类对象
```C++
class CT {
 public:
  using tfptr = void(*)(int);

  static void st_func(int t) {
    cout << "static function " << t << endl;
  }

  operator tfptr() {return st_func;}
};
int main() {
  using ptr = void(*)(int);
  CT ct;
  ct(5);
}
```

#### function
* 能够通过给它指定模版参数，能够以统一的方式来处理函数
```C++
  function<void(int)> ptr = func;
  ptr(3);

  function<void(int)> ptr2 = TC::st_func;
  ptr2(12);

  TC tc;
  function<void(int)> ptr3 = tc;
  ptr3(12);
```

#### bind
* 可调用对象和参数绑定到一起， 构成防函数
* 如果是多个参数，可以部分绑定
* bind对预先绑定的参数是通过值传递的

```C++
void func(int t) {
  cout << " t " << t << endl;
}

class CT {
 public:
  using tfptr = void(*)(int);

  static void st_func(int t) {
    cout << "static function " << t << endl;

  }

  operator tfptr() {return st_func;}

  void func(int x, int y) {
    m_a++;
    cout << "x " << x << " y " << y << " m_a "<< m_a << endl;
  }

  ~CT() { std::cout << " destory " << endl;}
  CT() { std::cout << " construct " << endl;}
  int m_a;
};

void bfunc(int x, int y, int z) {
  cout << "x " << x << " y " << y << " z " << z << endl;
}
void bfunc2(int& x, int& y) {
  x++;
  y++;
}

int main() {
  //绑定函数bfunc的参数30，placeholders::_1表示需要f1来参数
  auto f1 = bind(bfunc, placeholders::_1, placeholders::_1, 30);
  bind(bfunc, placeholders::_1, placeholders::_2, 30)(5, 15);
  f1(1);

  auto f3 = bind(bfunc, placeholders::_2, placeholders::_1, 30);
  f3(5,15);


  int a = 2, b = 3;
  //bind对预先绑定的参数是通过值传递的,所以a值是值传递的, 没有事先绑定的是引用传
  //递
  auto f4 = bind(bfunc2, a, placeholders::_1);
  f4(b);
  cout << "a " << a << " b " << b << endl;

  CT ct;
  ct.m_a = 1;
  //生成了防函数的临时对象，后续的func修改的是临时对象的m_a;
  //如果传的是对象引用，则不生成临时对象
  auto f5 = bind(&CT::func, ct, placeholders::_1, placeholders::_2);
  f5(10, 20);
  cout << "ct m_a = " << ct.m_a << endl;

  function<void(int,int)> f6 = bind(&CT::func, ct, placeholders::_1, placeholders::_2);
  f6(10, 223);

  cout << "=================" << endl;
  CT ct1;
  //第一次拷贝系统需要ct1产生临时对象，第二次是bind本身要返回临时对象，但是bind
  //执行完毕后，临时对象会被释放
  function<int &()> f7 = bind(&CT::m_a, &ct1);
  f7() = 60;
}
```