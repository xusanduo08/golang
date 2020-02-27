## http相关方法

`http.Get`、`http.Post`发出一个get或post请求。程序在使用完回复后必须关闭回复的主体。

`httputil.DumpResponse`()：转储一个回复

`http.NewRequest()`：创建一个请求

`request.Header.Add(key, value)`：添加header值

`http.DefaultClient`：一个默认的客户端

`http.DefaultClient.Do()`：发送请求

```go
func main() {
	req, err := http.NewRequest(http.MethodGet, "http://www.baidu.com", nil)

	if err != nil{
		panic(err)
	}
	client := http.DefaultClient
	resp, err :=client.Do(req)
	s, err :=httputil.DumpResponse(resp, true)
	if err != nil{
		panic(err)
	}
	fmt.Printf("%s \n", s)
}
```

`http.Client`： type struct，Client类型代表HTTP客户端，其零值为`DefaultClient`