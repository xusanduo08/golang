## 方法

**方法是包含了接受者的函数**，接受者可以是命名类型或者结构体类型的一个值或者一个指针。所有给定类型的方法属于该类型的方法集。

方法带有一个接受者，接受者在func关键字和方法名之间编写。

```go
func (t type) methodName(parameter list) (return list){

}
```

接受者可以在方法内部被访问到。

方法在调用时要指明接受者。

函数名不能相同，但方法名可以相同，只要接受者不同即可。

方法和函数对比：

* 方法：某个类别的方法，需要指定的接受者调用

  函数：一段独立的功能代码，可以直接调用

* 方法：方法名可以相同，只要接受者不同

  函数：函数名不能冲突

方法继承：方法可以继承，如果匿名字段实现了一个方法，那么包含这个匿名字段的struct也能调用该方法。

```go
type person struct{
	name string
	age int
}
type student struct{
  // person作为student的匿名字段，那么person的方法中不和student方法重名的也可以被student访问到
  // person作为student的匿名字段，所以person结构体中的字段即为student的提升字段
	person
	school string
}
func (p person) print(){
	fmt.Printf("使用person方法打印：%s", p.name)
}
func main() {
	s1 := student{
		person: person{"李四", 20},
		school: "清华",
	}
  //因为student中没有print方法，这地方s1是直接访问person的print方法，打印出：使用person方法打印：李四
	s1.print()
}
```

上例中，如果s1自己有print方法，那么`s1.print()`访问的就是s1自己的print方法：

```go
func (s student) print(){
	fmt.Printf("使用student方法打印：%s\n", s.school)
}
func main() {

	s1 := student{
		person: person{"李四", 20},
		school: "清华",
	}
  // 因为student自身有了print方法，所以这地方就运行的是自己的print：使用子类方法打印：清华
	s1.print()
}
```

