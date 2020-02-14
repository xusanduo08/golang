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

业务逻辑统一返回错误信息，外层套一个处理错误信息的函数统一处理错误。这样让业务专注于业务，错误处理专注于错误处理

下面实现的是通过url访问文件内容的服务器：

```go
type appHandler func(writer http.ResponseWriter, request *http.Request) error

// 统一处理错误
func errorWrapper(handler appHandler) func(writer http.ResponseWriter, request *http.Request){
	return func(writer http.ResponseWriter, request *http.Request) {
		err := handler(writer, request)
		if err != nil {
			log.Warn("Error handling request: %s", err.Error())
			code := http.StatusOK
			switch{
			case os.IsNotExist(err):
				code = http.StatusNotFound
			case os.IsPermission(err):
				code = http.StatusForbidden
			default:
				code = http.StatusInternalServerError
			}
			http.Error(writer,
				http.StatusText(code),
				code)
		}
	}
}

func main() {
	http.HandleFunc("/list/", errorWrapper(filelisting.Filelistfunc))
	err := http.ListenAndServe(":8888", nil)
	if err != nil {
		panic(err)
	}
}

// handler.go
// 返回error，error由errorWrapper处理
func Filelistfunc(writer http.ResponseWriter, request *http.Request) error{
	path := request.URL.Path[len("/list/"):]
	file, err := os.Open(path)
	if err != nil{
		return err
	}
	defer file.Close()

	all, err := ioutil.ReadAll(file)
	if err != nil{
		return err
	}
	writer.Write(all)
	return nil
}
```

