# Redis内部结构

#### 字符串
```C
struct SDS<T> {
  T capacity;
  T len;
  byte
}
```