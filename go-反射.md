

## 反射

> go语言提供了一种机制在运行时更新变量和检查它的值、调用他们的方法，但是在编译时并不知道这些变量的具体类型，这称为反射机制。

空接口`interface{}`：go的反射机制是通过接口进行的，类似于java的Object的空接口可以和任何类型进行交互，因此对基本类型数据等的反射也直接利用了这一特点。

go语言的类型：

* 变量包括（type，value）两部分
* type包括static type和conctete type。简单来说 static type是你在编码时看见的类型（如int、string），concrete type是runtime时系统看见的类型
* 类型断言能够成功，取决于变量的concrete type，而不是static type，因此，一个reader变量如果它的concrete type也实现了write方法，它也可以被类型断言为writer。

go语言的反射是建立在类型之上的，go的指定类型的变量的类型是静态的（也就是指定int、string这些的变量，它们的type是static type），在创建时就已经确定，反射主要与go的interface类型相关（它的type是concrete type），只有interface类型才有反射一说。



当向接口变量赋于一个实体类型的时候，接口会存储实体的类型信息，**反射就是通过接口的类型信息实现的，反射建立在类型的基础上**。

在go中，每个interface变量都有一个对应pair，pair中记录了实际变量的值和类型：

```go
(value, type)
```

value是实际变量值，type是实际变量的类型。一个interface{}类型的变量包含了2个指针，一个指针指向值的类型（对应concrete type），另外一个指针指向实际的值（对应value）。

例如，创建类型为*os.File的变量，然后将其赋给一个接口变量r：

```go
tty, err := os.OpenFile('../tty', os.O_RDWR, 0)

var r io.Reader
r = tty
```

接口变量r的pair中将记录如下信息：(tty, *os.File)，这个pair在接口变量的连续赋值过程中是不变的，将接口变量r赋给另一个接口变量w：

```go
var w io.Writer
w = r.(io.Writer)
```

接口变量w的pair与r的pair相同，都是(tty, *os.File)，即使w是空接口类型，pair也是不变的。

interface及pair的存在，是go中实现反射的前提。反射就是用来检测存储在接口变量内部pair对的一种机制。

### reflect包

#### 获取接口变量的Value对象：

`Reflect.ValueOf()`：获取输入参数接口中的数据的值对象，如果接口为空则返回0


#### 获取接口变量的Type对象：

`Reflect.TypeOf`：获取输入参数接口中的数据的类型对象，如果接口为空则返回nil

Type对象的常用方法：

`Kind()`：返回该接口的具体分类

`NumField`：返回struct类型的字段数（匿名字段算作一个字段），如非结构体将panic

`Name()String`：返回该类型在自身包内的类型名

`Field(i int) StructField`：返回struct类型的第i个字段的类型，如非结构体或者i不在[0, NumField()]内将会panic

`Method(i int) Method`：返回该类型方法集中的第i个方法

##### 通过反射对象获取接口信息：

* 如果是已知原有类型：

```go
realValue := value.Interface().(已知类型)


func main() {
	var num float64 = 1.23
	value := reflect.ValueOf(num)

	// 反射类型对象 ---> 接口类型变量
	convertValue := value.Interface().(float64)
	fmt.Println(convertValue)

	pointer := reflect.ValueOf(&num) // 传入指针类型
	convertPointer := pointer.Interface().(*float64) // 转换时也要选择指针类型
	fmt.Println(*convertPointer)
}
```

* 如果是未知类型：

先获取Type对象，通过Type对象的Field()方法获取字段（字段名及字段类型），再通过Value对象.Field(i).Interface()方法获取值。

```go
type Person struct{
   Age int
   Name string
   Gender string
}
func GetMessage(input interface{}){
	getType := reflect.TypeOf(input)
	getValue := reflect.ValueOf(input)

	/**
		1. 现获取Type对象：reflect.TypeOf
		2.通过Field()获取每一个Field字段
		3.Interface()，得到对应的Value
	**/
	for i := 0; i < getType.NumField(); i++{
		field := getType.Field(i) // 如果getType的Kind不是struct则会panic
		value := getValue.Field(i).Interface()
		fmt.Printf("字段名称: %s, 字段类型： %s, 字段值： %v\n", field.Name, field.Type, value)
	}
  
  // 获取方法
	for i := 0; i < getType.NumMethod(); i++{
		method := getType.Method(i)
		fmt.Printf("方法名称：%s, 方法类型: %v \n", method.Name, method.Type)
	}
}
func main() {
	person := Person{Name: "two dog", Age:30, Gender: "male"}
	GetMessage(person)
}
```

**注意：**go语言里面struct里面变量如果大写则是public,如果是小写则是private的，**private的时候通过反射不能获取其值**

**在反射概念中，编译时就知道变量类型的是静态类型，运行时才知道变量类型的叫做动态类型。**

#### 通过reflect.Value设置实际变量的值

reflect.Value是通过reflect.ValueOf(x)获得的，只有当x是指针时，才可以通过reflect.Value修改实际变量x的值。也就是说：要想修改一个变量的值，必须通过该变量的指针地址。

```go
func (v Value) Elem() Value
```

Elem返回v持有的接口保管的值的Value封装，或者v持有的指针指向的值的Value封装。如果v的Kind不是Interface或Ptr会panic；如果v持有的值为nil，会返回Value零值。

```go
func (v Value) serXxx() Value
```

设置v的持有值。如果v的Kind不是对应的Xxx，或者v.CanSet()返回false，则会panic

```go
func (v Value) CanSet() bool
```

如果v持有的值可以被修改，则返回true，否则返回false

```go
func (v value) FieldByName(name string) Value
```

返回该类型名为name的字段（的Value封装）（会查找匿名字段及其子字段），如果v的Kind不是Struct会panic；如果未找到会返回Value零值。

```go
func(v Value)Field(i int) Value
```

返回结构体的第i个字段（的Value封装）。如果v的Kind不是struct或i出界会panic



reflect.Value.Elem()表示获取原始值对应的反射对象。只有原始对象才能修改，当前反射对象是不能修改的，也就是说如果要修改反射类型对象，其值必须是“addressable”，对应的就是要传入指针，同时要通过Elem方法获取原始值对应的反射对象。

通过reflect.Value修改值：

* 获取Value对象：value := reflect.ValueOf(v)，v必须是个指针地址
* newValue := value.Elem()
* newValue.setXxx(xxx)

```go
func main() {
	var num float64 = 1.23
	getValue := reflect.ValueOf(&num) // 传入指针
  fmt.Println(getValue.Kind()) // ptr，所以如果直接getValue.setValue()的话会恐慌
	getValue.Elem().SetFloat(3.14)
	fmt.Println(getValue.Elem().Kind()) // float64
	fmt.Println(num) // 3.14
  
  person := Person{Name: "two dog", Age:30, Gender: "male"}

	value := reflect.ValueOf(&person)
  // value := reflect.ValueOf(person) // 直接传值的话后面的操作会报错
  // fmt.Println(value.Kind()) // struct
  // value.Elem() panic: reflect: call of reflect.Value.Elem on struct Value
  // 如果value的Kind不是Interface或Ptr则调用Elem方法会panic
	if value.Kind() == reflect.Ptr{
		newValue := value.Elem() // value的Kind必须是Ptr或Interface才能调用Elem方法
		fmt.Println(newValue.Kind()) // struct
		f1 := newValue.FieldByName("Name")
    // 可通过f1.Type()获知f1的字段类型
		f1.SetString("one dog")
		f2 := newValue.FieldByName("Age")
		f2.SetInt(20)
		f3 := newValue.FieldByName("Gender")
		f3.SetString("female")

		fmt.Println(person)
	}
}
```

**注意：**不管是修改基本类型数据还是引用类型数据，使用ValueOf获取反射对象时一定要传入地址

#### 通过Value.call调用方法，Value的Kind必须是func

先通过reflect.ValueOf方法获取反射对象，然后通过`MethodByName()`获取方法的reflect.Value封装对象，再判断其Kind是不是func，是则可以调用`Call()`执行函数。

`Call()`方法的参数是reflect.Value的切片，切片内数据分别对应着方法的参数。如果方法有返回值，那么`Call()`会返回所有结果的reflect.Value封装的切片。

```go
type Person struct{
	Age int
	Name string
	Gender string
}

func (p Person)Say(info string)string{
	fmt.Println(p.Name, info)
  return info
}

func (p Person) PrintInfo(){
	fmt.Printf("name: %s, age: %d, gender: %s", p.Name, p.Age, p.Gender)
}

func main() {
	person := Person{Name: "two dog", Age:30, Gender: "male"}
	GetMessage(person)
}

func GetMessage(input interface{}){
	
	getType := reflect.TypeOf(input)
	getValue := reflect.ValueOf(input)
	
	fmt.Printf("getValue Kind: %s\n", getValue.Kind())

	methodValue := getValue.MethodByName("Say")
  fmt.Printf("Kind: %s, 方法类型： %s", methodValue.Kind(), methodValue.Type())  // Kind: func
	args1 := []reflect.Value{reflect.ValueOf("hello world")}
  result := methodValue.Call(args1) // methodValue的Kind必须是func才能调用call
  fmt.Println(result[0].Kind()) // string
  fmt.Println(result[0].Interface().(string))
}
```

