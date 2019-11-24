# RTTI dynamic_cat typeid 虚函数表

#### RTTI
* 运行时类型识别
* dynamic_cast 能够将基类的指针转为派生类的指针或者引用
* typeid 返回指针或者引用指向的实际类型
* 想让RTTI正常工作，基类中必须有虚函数，只有虚函数存在才会使用动态类型
* 对于引用会抛出std::bad_cast

#### typeid
* 拿到对象类型信息，typeid返回常量对象的引用，对象类型是const std::typeinfo&
* 主要是为了比较两个指针指向的类型是否相同
* 指针定义的类型相同。不管new的是啥, typeid都相等

```
Human* h1 = new Men;
Human* h2 = new Women;
typeid(h1) == typeid(h2)
```
* 指针指向的类型是否相同

```
Men* h1 = new Men;
Woman* h2 = new Men;
typeid(*h1) == typeid(*h2)
```

*  只有基类有虚函数的时候，编译器才会对表达式求值得

#### typeinfo
* 