## 函数

* 变量名在前，类型在后。

* 可以一次返回多个值（多用在返回error的场景）

* 可以事先定义返回变量名称，但比较少用，当就函数体比较大时用起来并不友好，适用于简单函数

* 多个返回值常用于返回error

```go
func div(a, b int) (q, r int){ //定义返回变量名称
		q = a/b
		r = a % b
		return
}

func div(a, b int) (int, int){ // 一次返回多个值
  return a / b, a % b
}

func eval(a, b int, op string) (int, error){
  switch op{
    case "+":
    	return a + b, nil
    case "-":
    	return a + b, nil
    case "*":
    	return a + b, nil
    case "/":
    	return a + b, nil
    default:
    return 0, fmt.Errorf("unsupported operation:" + op)
  }
}

func main(){
  if result, err := eval(3, 4 "x"); err != nil{
    fmt.Println(err)
  } else {
    fmt.Println(result)
  }
}
```

* 函数是一等公民，函数的参数也可以是函数

函数式改写上面例子

```go
func apply(op func(int, int) int, a, b int) int{
	fmt.Printf("Calling %s with %d, %d\n", 
	runtime.FuncForPC(reflect.ValueOf(op).Pointer()).Name()), a, b)
	return op(a, b)
}

func main(){
  fmt.Println(apply( // 直接定义一个匿名函数
    func(a , b int) int{
      return int(math.Pow(float64(a), float64(b)))
    }, 3, 4
  ))
}
```

* 没有默认参数，没有可选参数，只有可变参数列表

```go
func sum(numbers ...int)int{
	s := 0
	for i := range numbers{
		s += numbers[i]
	}
	return s
}
```

