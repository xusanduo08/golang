## 结构体

结构体是由一系列具有相同或不同类型的数据构成的数据集合。结构体成员是由一系列的成员变量构成，这些成员变量也被称为“字段”。结构体可以用来存储数据。

（个人理解，结构体可以理解成是对象，后面也讲到了，结构体也可以定义方法，这就和对象更像了）

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

### 结构体修改值

直接使用点语法访问结构体内字段

```go
var node =&treeNode{}
(*node).right = &treeNode{value: 2}
// 也可以省略*号
node.left = &treeNode{value: 1}
```



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

**结构体为值类型**

### 值接收者/指针接收者

* 要改变内容必须使用指针接收者
* 结构过大也考虑使用指针接收者
* 一致性：如有指针接收者，最好都是指针接收者
* 值/指针接收者均可接受值/指针

### new()

内置函数new()，**专门用于创建某种类型的指针(分配内存并返回地址)**。

```go
type treeNode struct{
	value int
	left, right *treeNode
}
var left = new(treeNode) // new()返回一个指针
(*left).value = 8
fmt.Printf("%T\n", left) // *main.treeNode 指针类型
```

new(T)分配了**零值填充**的T类型的内存空间，**并返回其地址**。

```go
var left = new (treeNode)
var leftNil *treeNode // 没有分配内存

fmt.Printf("%v, %T\n", left, left) // &{0 <nil> <nil>}, *main.treeNode ---使用零值填充
fmt.Printf("%v, %T\n", leftNil, leftNil) // <nil>, *main.treeNode ---直接就是零值
```

内建函数make(T, args)只能创建slice、map和channel，并且返回一个有初始值（非零）的T类型，而不是*T。make返回初始化后的（非零）值。

关于new和make的区别，可以看[这里](https://www.flysnow.org/2017/10/23/go-new-vs-make.html)

### 匿名结构体

没有名字的结构体，创建结构体的同时，创建对象。只能使用一次，用处不大。

```go
s := struct{
	name string
	age int
}{
	name: "李四",
	age: 19
}
```

### 匿名字段

```go
type Worker struct{
	string // 匿名字段
	int // 匿名字段
}
// 匿名字段默认使用数据类型作为名字，所以匿名字段不允许重复，否则会冲突
worker2 := Worker{"王武", 20}
fmt.Println(worker2.string, worker2.int)
```

### 结构体嵌套

一个结构体可能包含一个字段，而这个字段反过来就是一个结构体。这种结构体称为嵌套结构体。

```go
type book struct{
	bookName string
	price float64
}
type student struct{
	name string
	age int
	book book
}
var b1 = book{"go语言", 50}
var s1 = student{
	name: "李四",
	age:  20,
  // 这地方是个值复制，会把b1中的数据复制一份传给这地方的book字段，在内存中s1.book和b1地址并不一样
  // 所以可以使用指针节省内存
	book: b1,
  //或者 book: book{"learn go", 49.3}
}
fmt.Println(s1) // {李四 20 {go语言 50}} 
// 修改时，如果字段是结构体，则可继续通过点语法访问或者修改
s1.book.bookName="十万个为啥" //值复制，这地方的修改不会影响到b1
fmt.Println(b1, s1) // {go语言 50} {李四 20 {十万个为啥 50}}
```

结构体嵌套时可以使用结构体指针节省内存。

```go
var b1 = book{"go语言", 50}
type student2 struct{
	name string
	age int
	book *book // 嵌套一个结构体指针
}
var s2 = student2{
	name: "王武",
	age:  20,
	book: &b1, // 取b1地址
}
fmt.Println(s2) // {王武 20 0xc00000c0c0}
s2.book.bookName = "红楼梦" // 因为使用的是指针，所以这地方会同步修改b1
fmt.Println(b1) // {红楼梦 50}

```

