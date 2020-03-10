## Select

**channel发送和接收数据都是阻塞式的，想实现非则塞效果可使用select**

`time.After()`：指定时间后向返回的channel中传入当前时间

`time.Tick()`：每隔指定时间返回一个channel

**select会随机执行一个可运行的case，如果没有case可运行，则会去执行default，没有的default的话，它将阻塞，直到有case可运行**

select语法：

* 每个case都必须是一个通信
* 所有channel表达式都会被求值
* 所有被发送的表达式都会被求值
* 如果任意某个通信可以进行，它就执行，其他被忽略
* 如果有多个case可以运行，select会随机的选择一个执行，否则：
  * 如果有default语句，则执行该语句
  * 如果没有default子句，select将阻塞，直到某个通信可以执行。go不会重新对channel或值进行求值

```go
func main() {
	c1 := make(chan int)
	c2 := make(chan int)

	go func(){
		time.Sleep(2*time.Second)
		c1<- 1
	}()
	go func(){
		time.Sleep(2*time.Second)
		c2 <- 2
	}()

	select{
	case n1:= <- c1:
		fmt.Printf("got %d from c1", n1)
	case n2:= <- c2:
		fmt.Printf("got %d from c2", n2)
	
	}
}
```



