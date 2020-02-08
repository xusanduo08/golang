### if

if条件中可声明变量，声明的变量只在if语句中有效

```go
if contents, err := ioutil.ReadFile(filename); err != nil{
	fmt.Println(err)
} else {
	fmt.Printf("%s \n", contents)
}
```

### switch

* switch会自动break，除非使用fallthrough
* switch之后可以没有表达式，在case之后带上表达式就行

### 循环

* for的条件里不需要括号
* 可以省略初始条件、结束条件、递增表达式

```go
func convertToBin(n int) string{
	result := ""
	for ; n > 0; n /= 2{
		lsp := n % 2
		result = strconv.Itoa(lsp) + result
	}
	return result
}
```

* 省略初始条件和递增表达式相当于while

```go
for scanner.Scan(){
	fmt.Println(scanner.Text())
}
```

* 都省略就是死循环

```go
for { // 死循环
	fmt.Println("abc")
}
```

