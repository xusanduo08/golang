## 包和封装

* 名字一般使用CamelCase
* 首字母大写：public
* 首字母小写：private

### 包

* 每个目录一个包
* main包包含可执行入口
* 为结构定义的方法必须放在同一个包内
* 可以是不同文件

### 如何扩充系统类型或者别人的类型

* 定义别名
* 使用组合

组合方式扩充类型：

```go
type Node struct{
	value int
	left, right *Node
}
type myTreeNode struct{
	node *Node
}
func (node Node) Print(){
	fmt.Println(node.value)
}
func (myNode myTreeNode) postOrder(){
	if myNode == nil || myNode.node == nil{
		return
	}
	left := myTreeNode{myNode.node.left}
	right := myTreeNode{myNode.node.right}
	
	left.postOrder()
	right.postOrder()
	myNode.node.Print()
}
```

定义别名方式扩充类型：

```go

type Queue []int

func (q *Queue) Push(v int){
	*q = append(*q, v)
}

func (q *Queue) Pop() int{
	head := (*q)[0]
	*q = (*q)[1:]
	return head
}

func (q *Queue) IsEmpty() bool{
	return len(*q) == 0
}

func main() {
	q := Queue{1}
	q.Push(2)
	q.Push(3)

	fmt.Println(q.Pop())
	fmt.Println(q.Pop())

	fmt.Print(q.IsEmpty())
	fmt.Println(q.Pop())
	fmt.Print(q.IsEmpty())
}
```





