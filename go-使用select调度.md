## Select

**channel发送和接收数据都是阻塞式的，想实现非则塞效果可使用select**

`time.After()`：指定时间后返回一个channel

`time.Tick()`：每隔指定时间返回一个channel

* select使用
* 定时器的使用
* 在select中使用nil channel

select语法：

* 每个case都必须是一个通信
* 所有channel表达式都会被求值
* 所有被发送的表达式都会被求值
* 如果任意某个通信可以进行，它就执行，其他被忽略
* 如果有多个case可以运行，select会随机的选择一个执行，否则：
  * 如果有default语句，则执行该语句
  * 如果没有default子句，select将阻塞，直到某个通信可以执行

```go
func generator() chan int{
	c := make(chan int)
	go func(){
		i := 0
		for{
			time.Sleep(time.Duration(rand.Intn(1500))*time.Millisecond)
			c<- i
			i++
		}

	}()
	return c
}

func worker(id int, c chan int){
	for n := range c{
		time.Sleep(time.Second)
		fmt.Printf("worker %d, received %d \n", id, n)
	}
}

func createWorker(id int) chan int{
	c := make(chan int)
	go worker(id, c)
	return c
}

func main() {
	var c1, c2 = generator(), generator()
	worker := createWorker(0)

	n := 0
	var values []int
	var tm = time.After(10*time.Second) // 10s后返回channel
	tick := time.Tick(time.Second) // 隔1s返回一个channel
	for{
		var activeWorker chan int
		var activeValue int
		if len(values) > 0 {
			activeWorker = worker
			activeValue = values[0]
		}
		select {
		case n = <-c1:
			values = append(values, n)
		case n = <-c2:
			values = append(values, n)
		case activeWorker<- activeValue:
			values = values[1:]
    case <- time.After(800 * time.Millisecond): // 超时
      fmt.Println("time out")
		case <- tick:
			fmt.Println("values length", len(values))
		case <-tm:
			fmt.Println("bye")
			return
		}
	}
}
```



