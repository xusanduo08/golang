## Map

### 声明

```go
var m1 = map[string]string{
	"course":"golang",
	"quality":"notbad",
}
m2 := make(map[string]string) // m2 is empty map
var m3 = map[string]int // m3 == nil
```

### 遍历

不保证遍历顺序，如需顺利，需手动对key排序

```
for k, v := range map{
	fmt.Println(k, v)
}
```

### 取值

```go
courseName, ok := m1["course"]
// var_name, ok := map[key] 如果key不存在，var_name为nil, ok为false
// 通过ok判断是否存在对应的键
if courseName, ok := m1["course"]; ok{
	fmt.Println(courseName)
} else {
	fmt.Println("key does not exist")
}
```

### 删除

```go
delete(m1, "course")
```

### 使用len获取元素个数

```
len(m1)
```

### map的key

* map使用哈希表，必须可以比较相等
* 除了slice、map、function的内建类型都可以作为key
* struct类型不包含上述字段，也可以作为key

