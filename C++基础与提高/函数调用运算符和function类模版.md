# 函数调用运算符和function类模版

#### function
* ()是函数调用运算符

#### 标准库funtion
```
int func(int i) {
  cout << "i " << i << endl;
  return i;
}

class A {
 public:
  int operator()(int i) {
    cout << "i " << i << endl;
    return i;
  }
};

int main() {
  function<int(int)> f1 = func;
  function<int(int)> f2 = A();
  A a;
  function<int(int)> f3 = a;
  f1(5);
  f2(5);
  f3(6);
  map<string, function<int(int)>> op = {
    {"f1", func},
    {"f2", A()},
    {"f3", a},
  };
  op["f1"](7);
  op["f2"](8);
  op["f3"](9);
}
```
* 函数func不能有重载,通过定义函数指针来解决

```
int(*func1)(int) = func;
function<int(int)> f1 = func1;
```
* 