## 正则表达式

* Regex.MustCompile
* Reg.FindAllString
* Reg.FindString
* Reg.FindAllStringSubmatch
* 

```go
func Compile(expr string) (*Regexp, error)
```

Compile解析并返回一个正则表达式。如果返回成功，该regexp就可用于匹配文本

```go
func MustCompile(str string) *Regexp
```

MustCompile类似Compile但会在解析失败时panic，主要用于全局正则表达式变量的安全初始化。

```go
func (re *Regexp) FindString(s string) string
```

Find返回保管正则表达式re在s中的最左侧的一个匹配结果的字符串。如果没有匹配到，会返回""；但如果正则表达式成功匹配了一个空字符串，也会返回""。如果需要区分这种情况，请使用FindStringIndex 或FindStringSubmatch。返回第一个符合匹配的结果字符串



```go
func (re *Regexp) FindAllString(s string, n int) []string
```

Find返回保管正则表达式re在s中的**所有**不重叠的匹配结果的[]string切片。如果没有匹配到，会返回nil。

```go
func main() {
   var text = `
   my email is ssss@google.com
   my email is ddd@qq.com
   my email is jggg@163.com
   `
   reg := regexp.MustCompile(`([a-zA-Z0-9]+)@([a-zA-Z0-9])+\.([a-zA-Z0-9]+)`)
   match := reg.FindAllString(text, -1)
   fmt.Println(match)
}
// [ssss@google.com ddd@qq.com jggg@163.com]
```

```go
func (re *Regexp) FindAllStringSubmatch(s string, n int) [][]string
```

Find返回一个保管正则表达式re在s中的所有不重叠的匹配结果及其对应的（可能有的）分组匹配的结果的[][]string切片。如果没有匹配到，会返回nil。

```go
func main() {
   var text = `
   my email is ssss@google.com
   my email is ddd@qq.com
   my email is jggg@163.com
   `
   reg := regexp.MustCompile(`([a-zA-Z0-9]+)@([a-zA-Z0-9])+\.([a-zA-Z0-9]+)`)
   match := reg.FindAllStringSubmatch(text, -1)
   fmt.Println(match)
}
// [[ssss@google.com ssss e com] [ddd@qq.com ddd q com] [jggg@163.com jggg 3 com]]
```