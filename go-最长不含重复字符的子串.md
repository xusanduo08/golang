## 最长不含重复字符的字串

```go
func noneRepeatingSubstr(s string) int{
	// 最长无重复字符子串长度
	// 1.记录每个字符出现过的位置x
	// 2. start为子串开始位置
	// 3.如果lastOccured出现在start之后，更新start、lastOccured、maxLength
	lastOccured := make(map[byte]int)
	start := 0
	maxLength := 0
	for i, ch := range []byte(s){
		if lastI, ok:=lastOccured[ch]; ok && lastI >= start{
			start = lastI + 1
		}
		if i - start + 1 > maxLength{
			maxLength = i - start + 1
		}
		lastOccured[ch] = i
	}
	return maxLength
}
func main() {
	fmt.Println(noneRepeatingSubstr("abcabcef"))
	fmt.Println(noneRepeatingSubstr(""))
	fmt.Println(noneRepeatingSubstr(("abc")))
	fmt.Println(noneRepeatingSubstr("abcb"))
}
```

