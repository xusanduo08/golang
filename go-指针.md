## 指针

### 指针

```go
var a int = 2
var pa *int = &a
*pa = 3
fmt.Println(a)
```

### 参数传递

**Go语言只有值传递一种方式**

交换两变量

错误做法：

```go
var a, b int = 3, 4
func swap(a, b int){
	b, a = a, b
}
```

因为是值传递，最终传入函数的只是原值的复制。正确做法：

```go
var a, b int = 3, 4
func swap(a, b *int){
	*b, *a = *a, *b
}
```

或者：

```go
var a, b int = 3, 4
func swap(a, b) (int, int){
	return b, a
}
func main(){
	a, b = swap(a, b)
}
```

第二种方法更好一些