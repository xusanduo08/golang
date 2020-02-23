## 传统同步机制

* WaitGroup
* Mutex
* Cond



```go
type atomicint int

func (a *atomicint) increment(){
	*a++
}

func(a *atomicint) get() int{
	return int(*a)
}

func main() {
	var a atomicint
	a.increment()
	go func(){
		a.increment()
	}()
	time.Sleep(time.Millisecond)
	fmt.Println(a)
}
```

使用go run -race atomic.go后发现其实有数据冲突。加锁后修改如下：

```go
type atomicint struct{
	value int
	lock sync.Mutex
}

func (a *atomicint) increment(){
	a.lock.Lock() // 上锁
	defer a.lock.Unlock()
	a.value++
}

func(a *atomicint) get() int{
	a.lock.Lock()
	defer a.lock.Unlock()
	return a.value
}

func main() {
	var a atomicint
	a.increment()
	go func(){
		a.increment()
	}()
	time.Sleep(time.Millisecond)
	fmt.Println(a.get())
}
```

通过匿名函数实现一个安全区域：

```go
func (a *atomicint) increment(){
	func(){
		a.lock.Lock() // 上锁
		defer a.lock.Unlock()
		a.value++
	}()
}
```

