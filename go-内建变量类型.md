## 内建变量类型

* bool, string
* (u)int, (u)int8, (u)int16, (u)int32, (u)int64, uintptr

加u是无符号整数

不加u是有符号整数

不规定长度的根据系统决定

* byte(8 位)， rune(字符型, 32位)

* float32, float64, complex64, complex128

### 类型转换

**类型转换是强制的，没有隐式转换**

```
func triangle(){
	a, b := 3, 4
	var c int
	c = int(math.Sqrt(float64(a * a + b * b)))
	fmt.Println(c)
}
```

`math.Sqrt`接收float64，返回float64。

