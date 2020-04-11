## OOP

### 提升字段

在结构体中属于匿名结构体的字段称为提升字段。因为它们可以被访问，就好像它们属于拥有匿名结构字段的结构体一样。

```go
type person struct{
	name string
	age int
}

type student3 struct{
	person
	school string
}
// person是student3的匿名字段，person中的name和age就是student3的提升字段，可以直接访问和修改
var s3 = student3{
	person: person{name:"wangwu", age: 12},
	school:"qinghua",
}
s3.name = "李四" // 相当于s3.person.name
s3.age = 15 // 相当于s3.person.age
fmt.Println(s3,s3.name, s3.name) // {{李四 15} qinghua} 李四 15
```

go中的结构体嵌套：

* 模拟继承性：is-a

```go
type A struct{
	field 
}
type B struct{
	A // A作为B的匿名字段
}
```

* 模拟聚合关系：has-a

```go
type C struct{
	field
}
type D Struct{
	c C // 非匿名字段，模拟聚合关系
}
```

