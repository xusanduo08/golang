## 结构体

### 声明一个结构体

```go
type treeNode struct{
	value int
	left, right *treeNode
}
```

### 使用结构体

```go
var root treeNode
root = treeNode{value: 3}
root.left = &treeNode{5, nil, nil}
root.right = &treeNode{value: 6}
```

### 使用工厂函数创建

```go
func createNode(value int) *treeNode{
	return &treeNode{value: value}
}
root.left.right = createNode(2)
```

* 使用自定义工厂函数
* 注意返回了局部变量的值

### 给结构体添加方法

```go
func (node treeNode) print(){
	fmt.Println(node.value)
}
func (node treeNode) setValue(value int){
	node.value = value
}
func main(){
	root := treeNode{value: 3}
	root.print() // 3
	root.setValue(5)
	root.print() // 3，依然是3
}
```

 因为go参数是值复制，所以上面的set方法应该使用**指针作为接收者**

```go
func (node *treeNode) setValue(value int){
	node.value = value
}
func main(){
	root := treeNode{value: 3}
	root.print() // 3
	root.setValue(5)
	root.print() // 5
}
```

* 只有使用指针才可以改变结构内容
* nil指针也可以调用方法

```go
func(node *treeNode) setValue(value int){
	if(node == nil){
		return
	}
	node.value = value
}
var pRoot *treeNode
pRoot.setValue()
```

### 值接收者/指针接收者

* 要改变内容必须使用指针接收者
* 结构过大也考虑使用指针接收者
* 一致性：如有指针接收者，最好都是指针接收者
* 值/指针接收者均可接受值/指针

