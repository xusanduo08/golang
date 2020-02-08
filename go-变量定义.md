## 变量定义

### var声明变量，可放在包内，或者函数内

```go
var a int // 变量名+类型，零值
var a = 3 // 编译器自动推断类型
var a, b = 3, false
var a, b int = 2, 3
var ( // 使用括号集中定义
	a = 2
	b = 3
)
```

### :=声明变量，只能在函数内使用

```go
a, b, c := 2, false, "string"
```



