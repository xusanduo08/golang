## 单元测试

`testing`

```go
func TestXxxx(*testing.T){}
```

Xxx开头必须是大写

**表格驱动测试**

add.go

```go
func add(a, b int) int{
	return a + b
}
```

add_test.go

```go
func TestAdd(t *testing.T){
	tests := [] struct{
		a int
		b int
		ans int
	}{
		{2, 3, 5},
		{4, 5, 9},
		{6, 7, 13},
		{8, 2, 11},
	}
	
	for _, tt := range tests {
		actual := add(tt.a, tt.b)
		if actual != tt.ans {
			t.Errorf("Got %d for input %d, %d, expected %d", actual, tt.a, tt.b, tt.ans)
		}
	}
}
```

testing.B：压力测试

testing.T：普通测试用例接口

testing.M：main测试使用的结构

Testing.Errorf：带格式打印错误信息

```go
func BenchmarkAdd(b *testing.B){
	test := struct{
		a int
		b int
		ans int
	}{2, 3, 5}

	for i := 0; i < b.N; i++{
		actual := add(test.a, test.b)
		if actual != test.ans{
			b.Errorf("Got %d for input %d, %d, expected %d", actual, test.a, test.b, test.ans)
		}
	}
}
```

或者在命令行执行：`go test -bench .`

查看测试报告：`go tool cover`

#### http测试工具

`httptest.NewRecoreder()`：返回一个response

`httptest.NewRequest()`：返回request指针