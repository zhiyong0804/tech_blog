# C++基础

#### 枚举类型
* 直接赋值整数不行

#### bool类型
* 枚举类型
```
typedef enum {
  false, true
}bool
```

#### 重载
* 返回值类型不同不可以构成重载
* 参数顺序不同可以重载
* 隐式转换尽量注意
* 底层实现 name mangling 函数签名
* .h里面mangling,但是库没有改变，extern c保证头文件不被mangling

#### 操作符重载
* +号的全局重载函数