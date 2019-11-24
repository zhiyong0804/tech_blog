# 可变参数函数&&initializer_list

#### 可变参数函数
* 这种能够接收非固定个数数的数就是可变参数还数
* initialiser_1 ist 标准库类型，该类型能够使用的前提条件是所有的实多类型相同
* 参数类型一致，但是个数不可预知

#### 拷贝和赋值
* 不会拷贝列表中元素，原来对象拷贝

#### 省略号形参
* 这种省略形参式的可变参数函数虽然参数数量不固定，但是函数的所有参数是存储在线性连续的空间中的
* 可变参数只能是字符串类型和int类型

```C++
double sum(int num, ...) {
  va_list valist;
  double sum = 0;
  va_start(valist, num);
  for (int i = 0; i < num; i++) {
    sum = sum + va_arg(valist, int);
  }
  va_end(valist);
  return sum;
}

void funcTest(const char* msg...) {
  va_list  valist;
  int cnt = atoi(msg);
  va_start(valist, msg);
  int args = 0;
  while (args < cnt) {
    char* p = va_arg(valist, char*);
    printf("args %d %s \n", cnt, p);
    args++;
  }
  va_end(valist);
}
```