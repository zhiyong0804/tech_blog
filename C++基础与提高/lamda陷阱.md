# lamda陷阱

#### 捕获异常
* 捕获列表中的：捕获外部作用域中所有变量，并作为引用在lambda 表达式中使用, 按照引用这种捕获方式，会导致 1 abda 表达式包含绑定到局部变量的引用

```C++
vector<function<bool(int)>> gv;

void func() {
  srand((int)time(NULL));

  int tmp = rand() % 6;

  gv.push_back([&](int v) {
                if (v % tmp == 0) {
                  return true;
                }
                return false;
               });
}

int main() {
  func();

  cout << gv[0](10) << endl;
}
```
* 引用捕获超过范围的情形也叫引用悬空
* 采用按值捕获可以解决引用悬空

#### 成员变量
* =是按值捕获的意思我们会认为这个[=]是按值捕获，使用我们能够访问成员变量tmp,所以我们顺理成量的认为，这里这个1ambda 表达式是所使用的tmp是按值捕获的
* lamda表达式的行正确与否，取決于pat対象是否存在,只有PAT对存在，这个lamda表达式行オ正确
* this指向对象本身，所以这里[=]捕获的是this指针值

```C++
vector<function<bool(int)>> gv;
class AT {
 public:
  vector<function<bool(int)>> gv;

  int tmp = 5;
  void addItem() {
    gv.push_back([=](auto v) {
                 if (v % tmp == 0) {
                 return true;
                 }
                 return false;
                 });
  };
};

int main() {
  func();


  AT* pat = new AT();
  pat->addItem();
  delete pat;
  cout << gv[0](10) << endl;
}
```
* 使用临时变量来捕获

```C++
vector<function<bool(int)>> gv;
class AT {
 public:
  vector<function<bool(int)>> gv;

  int tmp = 5;
  void addItem() {
    auto t_tmp = tmp;
    gv.push_back([t_tmp](auto v) {
                 if (v % t_tmp == 0) {
                 return true;
                 }
                 return false;
                 });
  };
};
```

#### 广义捕获
```C++
class AT {
 public:
  vector<function<bool(int)>> gv;

  int tmp = 5;
  void addItem() {
    gv.push_back([t_tmp = tmp](auto v) {
                 if (v % t_tmp == 0) {
                 return true;
                 }
                 return false;
                 });
  };
};
```

#### 静态局部变量
* 捕获不包括静态局部变量，但是可以使用
* 静态变量使用类似按引用捕获效果

```C++
vector<function<bool(int)>> gv;

void func() {
  srand((int)time(NULL));

  static int tmp = 5;

  gv.push_back([](auto v) {
                cout << " static val " << tmp << endl;
                if (v % tmp == 0) {
                  return true;
                }
                return false;
               });
  tmp++;
}
```