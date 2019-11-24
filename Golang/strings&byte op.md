# strings&byte op
#### 与string值相比，strings.Builder类型的值有哪些优势？
* 已经存在的内容不变，但是可以拼接更多的内容
* 减少了内存分配和内容拷贝的次数
* 可将内容重置，可重用值

#### string.Builder
* Write
* WriteByte
* WriteRune
* WriteString

#### string.Builder约束
* 绝不共享Builder值以及它的指针值。

#### 为什么说strings.Reader类型的值可以高效地读取字符串？
* Reader值可以让我们很方便地读取一个字符串中的内容。它的高效主要体现在它对字符串的读取机制上。在读取的过程中，Reader值会保存已读取的字节的计数，也称已读计数。

#### byte.Buffer
* 使用字节切片作为内容机器
* int类型代表已读字节的计数
* Buffer值的长度是未读内容的长度，而不是已存内容的总长度。它与在当前值之上的读操作和写操作都有关系
* Buffer值的容量指的是它的内容容器（也就是那个字节切片）的容量，它只与在当前值之上的写操作有关，并会随着内容的写入而不断增长。

#### byte.Buffer已读计数的作用
* 读取内容时，相应方法会依据已读计数找到未读部分，并在读取后更新计数。
* 写入内容时，如需扩容，相应方法会根据已读计数实现扩容策略。
* 截断内容时，相应方法截掉的是已读计数代表索引之后的未读部分。
* 读回退时，相应方法需要用已读计数记录回退点。
* 重置内容时，相应方法会把已读计数置为0。
* 导出内容时，相应方法只会导出已读计数代表的索引之后的未读部分。
* 获取长度时，相应方法会依据已读计数和内容容器的长度，计算未读部分的长度并返回。

#### 对比strings.Builder和bytes.Buffer的String方法，并判断哪一个更高效？原因是什么？
* strings.Builder String方法中\*(\*string)(unsafe.Pointer(&b.buf)) 是直接取得buf的地址然后转换成string返回。而bytes.Buffer的String方法是 string(b.buf[b.off:])然后对buf进行操作

#### 案例
```go
const numbers = 100

func BenchmarkSprintf(b *testing.B) {
	b.ResetTimer()
	for idx := 0; idx < b.N; idx++ {
		var s string
		for i := 0; i < numbers; i++ {
			s = fmt.Sprintf("%v%v", s, i)
		}
	}
	b.StopTimer()
}

func BenchmarkStringBuilder(b *testing.B) {
	b.ResetTimer()
	for idx := 0; idx < b.N; idx++ {
		var builder strings.Builder
		for i := 0; i < numbers; i++ {
			builder.WriteString(strconv.Itoa(i))

		}
		_ = builder.String()
	}
	b.StopTimer()
}

func BenchmarkBytesBuf(b *testing.B) {
	b.ResetTimer()
	for idx := 0; idx < b.N; idx++ {
		var buf bytes.Buffer
		for i := 0; i < numbers; i++ {
			buf.WriteString(strconv.Itoa(i))
		}
		_ = buf.String()
	}
	b.StopTimer()
}

func BenchmarkStringAdd(b *testing.B) {
	b.ResetTimer()
	for idx := 0; idx < b.N; idx++ {
		var s string
		for i := 0; i < numbers; i++ {
			s += strconv.Itoa(i)
		}

	}
	b.StopTimer()
}
```