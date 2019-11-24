# 引用折叠&转发&完美转发&forward

#### 引用折叠
```
template<typename T>
void func(T &&tmp)

int main(){
  int i = 18;
  func(i); //i左值 T int& tmp int&
  func(100);//i 右值  T int tmp int&&
  void func(int& &&tmp) {} 
  void func(int& tmp) {} //编译器展现出来的
}
```
* 引用折叠
* 左值引用&, 右值引用&&
* T是第一组左值引用， &&tmp第二组右值引用

| 第一组 | 第二组 | 结果 |
| :-: | :-: | :-: |
| &左值引用 | &左值引用 | &左值引用 |
| &左值引用 | &&右值引用 | &左值引用 |
| &&右值引用 | &左值引用 | &左值引用 |
| &&右值引用 | &&右值引用 | &&右值引用 |

* 折叠规则，只要有任意一个引用为左值引用，结果就是左值引用，否则就是右值引用

#### 引用的引用
```
int b = 500;
int &rb = b;
int & &rrb = b; //引用的引用，两个&之间有空格
```
* 引用的引用是非法的
* 编译器内部推断

#### 转发和完美转发
* 函数模版把收到的参数以及这些参数的类型转发给其他函数

```
void func2(int t1, int& t2) {
  cout << " t1 + t2 " << t1 + t2 << endl;
  t2++;
}
template<typename F, typename T1, typename T2>
void func(F f, T1 t1, T2&& t2) {
  f(t1, t2)
}
```
* 要保持t2的引用属性，T2改成万能引用T2&&，可以推导所有的实参类型
* T2改成T2&不能保留左值或者右值属性

```
 int&& rval = 80;//右值引用必须要绑定右值, &&rval是右值引用,但是rval本身是个左值
  func3(rval, j);//err
  func3(80, j);//right
```


#### 完美转发
```
void func3(int&& t1, int& t2) {
  cout << " t1 + t2 " << t1 + t2 << endl;
  t2++;
}

template<typename F, typename T1, typename T2>
void func(F f, T1&& t1, T2&& t2) { //t1是左值, 形参都是左值
   f(forward<T1>(t1), forward<T2>(t2));
}

int main() {
  int i = 20, j = 30;
  func(func2, i, j);
  func3(70, j);
  cout << " j " << j << endl;

  int&& rval = 80;//右值引用必须要绑定右值, &&rval是右值引用,但是rval本身是个左值
  //func3(rval, j);
  func(func3, 80, j);
}
```
* 接受任意实参的函数模板
* 目标函数会接收到与转发函数所收到的完全相同
* std::forward
* 发挥作用的条件
* 调用模板函数，模板函数参数是万能引用类型，模版函数负责转发
* std::forward的能力就是按照参数本来的类型转发
* 实参如果原来是左值，forward还是按照原来的类型处理
* 实参如果原来是右值，形参变成了左值，forward会把左值信息还原为右值
* 万能引用会保存实参的左值右值的信息，

```
void func4(int &t) {
  cout<< "left  value " << endl;
}

void func4(int &&t) {
  cout<< "right  value " << endl;
}

template <typename T>
void testF(T&& t) {
  func4(t);
  func4(forward<T>(t));
  func4(move(t));//move是左值转右值。
}
```
* forward是有条件的转发，左值转右值
* move无条件强制类型转换成右值

#### 万能引用
* 