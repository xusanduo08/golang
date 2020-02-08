## 数组

定义

```
var arr1 [5]int // 会自动初始化5个0
arr2 := [3]int{1, 3, 5}
arr3 := [...]int{2,4,6,8,10}
var grid [4][5]int // 二维数组
```



数组遍历：**range**

```
for i := 0; i < len(arr3); i++{
	fmt.Println(arr3[i])
}
// 或者range，或者索引和值，若只想获取值，可用_代替i位置
for i, v := range arr3{
	fmt.Println(i, v)
}

for _, v := range arr3{
	fmt.Println(v)
}
```

### 数组是指类型

下面函数的参数是`arr [5]int`，表示入参是一个数组，和`arr []int`不一样，这表示入参是一个切片。以数组为入参时，长度也是类型的一部分，如果长度不一样，则会报错

```go
func printArray(arr [5]int){
	for i, v := range arr{
		fmt.Println(i, v)
	}
  arr[0] = 100 // 数组是值类型，在函数内部对数组做的更改不会影响外部。当然如果传入的是数组指针就不一样了
}

var arr1 [5]int
arr2 := [3]int{1, 3, 5}
arr3 := [...]int{2, 4, 6, 8, 10}
func main(){
	printArray(arr1)
  printArray(arr2) // cannot use arr2 (type [3]int) as type [5]int in argument to printArray
	printArray(arr3)
}
```

* [10]int和[20]int是不通类型
* 调用`func f(arr [10]int)`会拷贝数组
* 在go语言中一般不直接使用数组

