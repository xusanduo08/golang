## 函数与闭包

### 函数式编程和函数指针

* 函数是一等公民：参数、变量、返回值都可以是函数
* 高阶函数
* 闭包

### “正统”函数式编程

* 不可变性：不能有状态，只有常量和函数
* 函数只能有一个参数

实现一个递加求和函数

```go
// 使用闭包
func adder(v int) func(int) int{
	sum := 0
	return func(v int) int{
		sum += v
		return sum
	}
}

// 使用正统函数式编程
type iAdder func(int) (int, iAdder)
func adder2(base int) iAdder{
  return func(v int)(int, iAdder){
    return base + v, adder2(base + v)
  }
}


func main(){
  a := adder2(0)
  for i := 0; i < 10; i++{
    var s = 0
    s, a = a(i)
    fmt.Println(s)
  }
}

```

### 闭包的应用

#### 斐波那契数列

```go
func fibonacci() intGen{
	a, b := 0, 1
	return func() int{
		a, b = b, a + b
		return a
	}
}
```

#### 遍历二叉树

中序遍历每个节点，对每个节点执行指定函数

```go
type Node struct{
	value int
	left, right *Node
}

func (node *Node)TraverseFunc(f func(*Node)){
	if node == nil {
		return
	}
	node.left.TraverseFunc(f)
	f(node)
	node.right.TraverseFunc(f)
}
func main(){
	var root Node
  root = Node{value: 3}
  root.left = &Node{}
  root.right = &Node{5, nil, nil}
  root.right.left = new(Node)
  root.left.right = &Node{value: 2}
  count := 0
  
  root.TraverseFunc(func(n *Node){ // 计算节点数量
    count++
  })
  
  fmt.Println(count) // 5
}
```



相比其他语言，go的闭包：

* 更为自然，不需要修饰如何访问自由变量
* 没有Lambda表达式，但是有匿名函数