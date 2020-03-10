## time包中通道相关函数

标准库中的timer让用户可以定义自己的超时逻辑，尤其是在应对select处理多个channel的超时、单channel读写的超时等情形时尤为方便。

Timer是一次性的时间触发事件，这点与Ticker不同，Ticker是按一定时间间隔持续触发时间事件。

Timer常见的创建方式：

```go
t := time.NewTimer(d)
t := time.AfterFunc(d, f)
c := time.After(d)
```

Timer有3个要素：

```
定时时间：就是那个d
触发动作：就是那个f
时间channel：也就是t.C
```

`Timer`是个结构体：

```
type Timer{
	C <- chan Time
}
```



### 一、time.NewTimer()

NewTimer()创建一个新的计时器，该计时器将在至少持续d时间之后发送当前时间到其自身的C字段。

```go
func main() {
	timer := time.NewTimer(3 * time.Second)
	fmt.Println(time.Now())

	fmt.Println(<- timer.C)
}
```

还未完成的计时器可以取消：`Stop()`

```go
func main() {
	timer := time.NewTimer(5 * time.Second)
	fmt.Println(time.Now())
	go func(){
		<- timer.C
	}()
	flag := timer.Stop()
	if flag {
		fmt.Println("timer has been canceled")
	}
	fmt.Println(<- timer.C)
}
```

### 二、timer.After()

相当于NewTimer(d).C，经过指定时间后向返回值发送当前时间

```go
func main() {
	timerChan := time.After(5 * time.Second)
	fmt.Println(time.Now()) // 2020-03-10 13:59:42.232208 +0800 CST m=+0.000083312

	fmt.Println(<- timerChan) // 2020-03-10 13:59:47.236101 +0800 CST m=+5.003920853
}
```

源码：

```go
func After(d Duration) <-chan Time {
	return NewTimer(d).C
}
```

