# sql函数

#### 分类
* 算术函数
* 字符串函数
* 日期函数
* 转换函数

#### 算术函数
* ABS
* MOD
* ROUND

#### 日期函数
* ![3dec8d799b1363d38df34ed3fdd29045](media/15660857101922/3dec8d799b1363d38df34ed3fdd29045.png)

* datetime类型和string类型比较不安全
* 无法确认 birthdate 的数据类型是字符串，还是 datetime 类型，如果你想对日期部分进行比较，那么使用DATE(birthdate)来进行比较是更安全的