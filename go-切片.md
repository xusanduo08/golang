## 切片

切片几种写法：

```go
arr := [...]int{0,1,2,3,4,5,6,7,8,9}
s := arr[2:6]
s := arr[:6]
s := arr[2:]
s := arr[:]
```

* slice本身没有数据，是对底层array的一个view

```go
arr := [...]int{0,1,2,3,4,5,6,7,8,9}
s := arr[2:6]

s[0] = 10 // 此时arr变成[0, 1, 10, 3, 4, 5, 6, 7, 8, 9]，s[0]相当于arr[2]
```

### reslice

relice时，下标是针对自身的

```
s := arr[2:6]
s = s[:3]
s = s[1:]
s = arr[:]
```

### slice扩展

```
arr := [...]int{0,1,2,3,4,5,6,7}
s := arr[2:6]
s2 := s[3:5]
```

s2的值是多少？s2=[5, 6]

* slice可以向后扩展，不可以向前扩展
* s[i]不可以超越len(s)，向后扩展不可以超越底层数组cap(s)

slice的容量就是slice的开头元素到底层数组末尾的长度

### 向slice添加元素

* 添加元素时如果超越cap，系统会重新分配更大的底层数组
* 如果没超过cap的话，那么底层数组的值也会一同改掉
* 由于值传递的关系，必须接收append的返回值
* s = append(s, val)

```
arr := [...]int{0,1,2,3,4,5,6,7}
s := arr[2:6] // 2 3 4 5 
s2 := s[3:5] // 5 6
s2 = append(s2, 10) // 5 6 10，没超过cap，底层数组的值也会改掉
fmt.Println(arr) // [0,1,2,3,4,5,6,10]
```

### 切片创建

```
s := [] int
s := []int{2,4,6,8}
s := make([]int, 16)
s := make([]int, 16, 32) // 长度16，容量32
```

### 切片拷贝

```
copy(destination, source)
s1 := make([]int, 16)
s2 := make([]int, 16, 32)
copy(s2, s1)
```

### 切片删除

删除s2索引为3的元素：

```
s2 = append(s2[:3], s2[4:])
```

删除头部元素：

```
s2 := s2[1:]
```

删除尾部元素

```
s2 := s2[:len(s2) - 1]
```

