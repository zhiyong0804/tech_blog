# interface
#### 接口变量赋值后发生什么
* 接口被赋予实际值之前，他的值是nil
* 接口赋值后，变量动态类型和动态值一起被存储在一个专用的数据结构里iface
* iface有两个指针，一个指向类型信息，一个指向动态值的指针

#### 接口之间的组合
```go
type Animal interface {
	ScientificName() string
	Category() string
}

type Pet interface {
	Animal
	Name() string
}
```
#### 转型和判断类型
```go
reflect.TypeOf(pet).String() //判断类型
interface{}(pet).(Dog) //转型
```
##### 接口静态类型和动态类型
* 静态类型就是接口名的类型
* 动态类型就是动态变量的类型

```go
  type InterfaceStructure struct {
    pt uintptr // 到值类型的指针
    pv uintptr // 到值内容的指针
}
```

#### nil复制给接口变量
* nil 的实现类型赋给了接口变量，该变量接口仍然可以调用该接口的方法吗

#### 自定义error
```go
  type RedisError string
  Error() string { return "Redis Error: " + string(err) }
```

* 自定义结构体error

```go
package main
import (
//	"fmt"
)
type PathError struct {
	Op   string
	Path string
	err string
}
func (e *PathError) Error() string {
	return e.Op + " " + e.Path + ": " + e.Err.Error()
}
func test() error {
	return &PathError{
		Op:   "op",
		Path: "path",
	}
}
func main() {
	test()
}
```