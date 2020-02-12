## 接口

* **接口由使用者定义**
* **接口的实现是隐式的**
* **只要实现接口里的方法**

```go
type Retriever interface{
	Get(url string) string
}
func download(r Retriever) string{
	return r.Get("www.baidu.com")
}
```

### 接口的值类型

```go
package "mock"

type Retriever struct{
	contents string,
}
func (r Retriever) Get(url string) string{
	return r.contents
}

package "main"

type Retriever interface{
	Get(url string) string
}
func main(){
	var r Retrever
	r = mock.Retriever{"This is fake"}
  fmt.Printf("%T %v", r, r) // mock.Retriever {"This is fake"}
}
```

### 	获取接口变量类型

#### switch r.(type)

```go
switch r.(type){
case mock.Retriever:
	fmt.Println("Contents:", r.Contents)
case real.Retriever:
	fmt.Println("Useragent:", r.Useragent)
}
```

#### type assertion

```go
if mockTriever, ok := r.(mock.Triever); ok{
	fmt.Println(mockTriever.Contents)
} else {
	fmt.Println("Not a mock retriever")
}
```

### 接口变量内部有什么

* 实现者类型
* 实现者指针或者值
* **接口变量**自带指针
* **接口变量**同样采用值传递，几乎不需要使用接口的指针
* 指针接收者实现只能以指针方式使用；值接收者都可以

### interface{}表示任何类型

```go
type Queue []interface{} // 声明了一个任意类型的切片

func (q *Queue) Push(v interface{}){
	*q = append(*q, v)
}
func (q *Queue) Pop() interface{}{
	head := (*q)[0]
	*q = (*q)[1:]
	return head
}
```

### 接口的组合

```go
type Retriever interface{
	Get(url string) string
}
type Poster interface{
	Poster(url string, form map[string]string) string
}

type PosterRetriever interface{ //将两个接口组合在一起
	Retriever
	Poster
}
```

### 常用系统接口

* `Stringer`
  * `String()`方法
* `Reader`
* `Writer`