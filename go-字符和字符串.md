## 字符和字符串

```go
func main(){
	s := "Yes 我爱Lisa"
	fmt.Printf("len(s) = %d ", len(s))
}
```

`len(s)`计算出来的结果是14。golang中string底层是通过byte数组实现的，且golang默认的编码为utf-8，中文占3个字节长度，所以最终长度是14。

**如何获取字符串真实长度而不是其底层字节数组长度？utf8.RuneCountInString**

```go
func main(){
	s := "Yes 我爱Lisa"
	fmt.Printf("rune count in string = %d ", utf8.RuneCountInString(s))
}
```

**打印字符串底层字节数组**：[]byte获得字符串底层字节数组

```go
func main(){
	s := "Yes 我爱Lisa"
	for _, b := range []byte(s){
		fmt.Printf("%X", b)
	}
}
// 输出的是utf-8的编码
// 59 65 73 20 E6 88 91 E7 88 B1 4C 69 73 61
```

**打印unicode编码**：使用range遍历pos、rune对

```go
func main(){
	s := "Yes 我爱Lisa"
  // 先使用utf-8解码，然后转成unicode方式放到4字节的rune类型中
	for i, ch := range s{ // ch is a rune
		fmt.Printf("(%d, %X)", i, ch)
	}
}
// (0 59) (1 65) (2 73) (3 20) (4 6211) (7 7231) (10 4C) (11 69) (12 73) (13 61)
```

**utf8.DecodeRune(s)**：返回s的第一个rune值和所占用的字节数

```go
for len(s) > 0{
	ch, bytes := utf8.DecodeRune([]byte(s))
	s = s[bytes:]
	fmt.Printf("%c ", ch)
}
// Y e s   我 爱 L i s a
```

**转成rune数组，逐个打印字符，获取每个字符的索引**

```
for i, ch := range []rune(s){
	fmt.Printf("(%d %c)", i, ch)
}
```

* 使用range遍历pos、rune对
* 使用utf8.RuneCountInString获得字符数量
* 使用len获得字节长度
* **使用[]byte获得字节**数组

### 字符串工具包：strings

其他字符串操作：

* Fields、Split、Join
* Contains、Index
* ToLower、ToUpper
* Trim、TrimRight、TrimLeft