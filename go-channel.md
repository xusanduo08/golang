## channel

goroutine之间的交互使用channel进行

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