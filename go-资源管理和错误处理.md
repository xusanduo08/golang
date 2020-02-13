### 资源管理和错误处理

### defer

* 确保调用在函数结束时发生

多个defer语句想象成一个栈，语句先进后出的执行

```go
func main(){
	tryDefer() // 3 2 1
}

func tryDefer(){
	defer fmt.Println(1)
	defer fmt.Println(2)
	
	fmt.Println(3)
}
```

* 参数在defer语句时计算

```go
func main(){
	tryDefer() // 打印出3 2 1 0 和panic，而不是打印3次3，也就是说参数在defer语句时就计算好了
}

func tryDefer(){
  for i := 0; i < 10; i++{
    defer fmt.Println(i)
    if i == 3{
      panic("printed too many")
    }
  }
}
```

### 何时使用defer调用

* Open/Close
* Lock/Unlock
* PrintHeader/PrintFooter

### 错误处理

error是一个接口

```
type error interface {
	Error() string
}
```

自定义error: **`errors.New()`**

```go
err := errors.New("This is a custom error")
```

错误处理

```go
file, err := os.Open("abc.txt")
if err != nil{
	if pathError, ok := err.(*.os.PathError); ok{
		fmt.Println(pathError.Err)
	} else {
		fmt.Println("unknown error", err)
	}
}
```

已知类型错误处理掉，未知类型可以打印出来

### 错误处理：如何实现统一的错误处理逻辑

