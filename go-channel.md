## channel

**goroutine之间的通道就是channel**

**`var  c chan int`这样只是声明了一个类型为`chan int`的变量，它的值还只是`nil`，除了在`select`中是没法用的 **。

可以通过`make`来创建一个channel

```go
c := make(chan int)
```

这样创建出来的channel是可以用的

**发送和接收默认时阻塞的：**

一个通道发送和接收数据默认是阻塞的。当一个数据被发送到通道时，在发送语句中被阻塞，直到另一个goroutine从该通道读取数据。相对的，当从通道读取数据时，读取被阻塞，直到一个goroutine将数据写入该通道。



发给channel的数据如果没有goroutine接收程序会deadlock：

```go
func chanDemo(){
	c := make(chan int)
	go func(){ // 使用goroutine接收发给channel的数据
		for{
			n := <-c
			fmt.Println(n)
		}
	}()
	c <- 1
	c <- 2
	time.Sleep(time.Millisecond)
}
```

channel也能作为参数，也能作为返回值

```go
// 返回一个只能发送数据的channel
func createWorker(id int) chan<-int{
	c := make(chan int)
	go func(){
		for{
			fmt.Printf("worker id %d, received %d", id, <-c)
		}
	}()
	return c
}

func chanDemo(){
	var channels [10]chan<-int
	for i := 0; i < 10; i++{
		channels[i] = createWorker(i)
	}
	for i := 0; i < 10; i++{
		channels[i] <- i
	}
	time.Sleep(time.Millisecond)
}
```

给channel方向：

`chan<-int`：只能发送数据

`<-chan int`：只能接收数据

**死锁：**

如果goroutine在一个通道上发送数据，那么预计其他的goroutine应该接收数据。如果这种情况不发生，那么程序将在运行时出现死锁。



### 缓冲区

缓冲区，发送给channel的数据不必立马接收。

发送到缓冲通道的数据只有在缓冲区满时才被阻塞，类似的，从缓冲通道接收的信息只有在缓冲区为空时才会被阻塞。

```go
func bufferedChannel(){
	c := make(chan int, 3) // 缓冲区大小为3，可以发送3个数据，超过3个且没有接收程序会deadlock
	c <- 1
	c <- 2
	c <- 3
}
```

#### 告诉接收方数据发送完毕：close channel

**由发送方去close**：`Close(channel)`

```go
func bufferedChannel(){
	c := make(chan int, 3) // 缓冲区大小为3，可以发送3个数据，超过3个且没有接收程序会deadlock
	c <- 1
	c <- 2
	c <- 3
	Close(c) // 关闭channel
}
```

### 接收方判断channel是否close

channel关闭后再读取channel的值就是channel类型的零值。

 第一种方法：接收者可以在接收来自通道的数据时使用额外的变量来检查通道是否已关闭

```go
n, ok := <- c
if !ok{
	break
}
```

第二种方法：使用range循环从通道上获取数据，直到通道关闭。自动去检测channel是否close，channel关闭或者数据读完之后循环自动结束

```go
for n := range c{
	// TODO....
}
```



**Don't communicate by sharing memory; share memory by communicating**

**不要通过共享内存来通信，通过通信来共享内存**

以上例子中需要通过`time.Sleep(time.Millisecond)`才能打印出内容，想办法在打印结束后通知外界打印完毕。

通过通信来共享内存

```go
func main() {
	chanDemo()
}

func doWorker(id int, c chan int, done chan bool){
	for n := range c{
		fmt.Printf("worker id %d, received %c \n", id, n)
		done <- true
	}
}

type worker struct{
	in chan int
	done chan bool
}
func createWorker(id int) worker{
	w := worker{
		in: make(chan int),
		done: make(chan bool),
	}
	go doWorker(id, w.in, w.done)
	return w
}


func chanDemo(){
	var workers [10]worker
	for i := 0; i < 10; i++{
		workers[i] = createWorker(i)
	}
	for i, work := range workers{
		work.in <- 'a'+ i
	}
	for _, work := range workers{
		<-work.done // wait
	}
	for i, work := range workers{
		work.in <- 'A' + i
	}
	for _, work := range workers{
		<-work.done // wait
	}
}
```

**使用`sync.WaitGroup`完成：**

* Add(delta int)：任务数量
* Done()
* Wait()

```go
func main() {
	chanDemo()
}

func doWorker(id int, c chan int, wg *sync.WaitGroup){
	for n := range c{
		fmt.Printf("worker id %d, received %c \n", id, n)
		wg.Done() // 结束任务
	}
}

type worker struct{
	in chan int
	wg *sync.WaitGroup // 使用指针
}
func createWorker(id int, wg *sync.WaitGroup) worker{
	w := worker{
		in: make(chan int),
		wg: wg,
	}
	go doWorker(id, w.in,w.wg)
	return w
}


func chanDemo(){
	var workers [10]worker
	var wg sync.WaitGroup

	for i := 0; i < 10; i++{
		workers[i] = createWorker(i, &wg)
	}

	wg.Add(20) // 添加任务数量
	for i, work := range workers{
		work.in <- 'a'+i
    // wg.Add(1) // 或者在这里Add也行
	}

	for i, work := range workers{
		work.in <- 'A' + i
	}
	wg.Wait()
}
```

抽象关闭任务的操作：

```go
func doWorker(id int, w worker){
	for n := range w.in{
		fmt.Printf("worker id %d, received %c \n", id, n)
		w.done() // worker里只管调用done方法，具体done是什么逻辑不用关心
	}
}

type worker struct{
	in chan int
	//wg *sync.WaitGroup // 使用指针
	done func()
}
func createWorker(id int, wg *sync.WaitGroup) worker{
	w := worker{
		in: make(chan int),
		done: func(){wg.Done()}, // 关闭操作放在这
	}
	go doWorker(id, w)
	return w
}
```

