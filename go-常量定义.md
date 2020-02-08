## 常量定义

### 常量

使用const定义

常量数值可作为各种类型使用（没有规定类型时）

```go
const filename = "abc.txt"
const a, b = 3, 4 // 此时没有定义类型，变量类型为不确定，既可以做int也可以做float
var c int
c = int(math.Sqrt(a * a + b * b)) // 此时a b当作float使用
```

也可以使用括号集中定义

```
const (
	filename = "abc.txt"
	a, b = 3, 4
)
```

### 枚举

使用const块定义

```
const (
	cpp = 1
	java = 2
	golang = 3
)
```

**`iota`**：自增值

```go
const (
	cpp = iota
	java
	golang
)
fmt.Println(cpp, java, golang) // 0, 1, 2
```





回顾：

* 变量类型写在变量名之后
* 编译器可推测变量类型
* 没有char，只有rune
* 原生支持负数类型