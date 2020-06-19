# Leet Code 解题

- [Leet Code 解题](#leet-code-解题)
  - [125. 验证回文串](#125-验证回文串)

## 125. 验证回文串

给定一个字符串，验证它是否是回文串，只考虑字母和数字字符，可以忽略字母的大小写。

**说明**：本题中，我们将空字符串定义为有效的回文串。

示例1：
```
输入: "A man, a plan, a canal: Panama"
输出: true
```

示例2：
```
输入: "race a car"
输出: false
```

**解题思路**：可以从头和尾同时遍历字符串，遇到非字符的直接跳过，当遇到字符时进行对比是否一致，如果不一致直接返回false，当头指针和尾指针相遇的时候，就说明是回文串

**实现**
```go
func isPalindrome(s string) bool {
	str := strings.ToLower(s)
	i := 0
	j := len(str) - 1
	for i < j {
		for i < len(str) && !isChar(str[i]) {
			i++
		}
		for j > 0 && !isChar(str[j]) {
			j--
		}

    // 如果是空字符串
    if i >= len(str) || j < 0 {
        return true
    }
		if str[i] != str[j] {
			return false
		}
		i++
		j--
	}
	return true
}

func isChar(c byte) bool {
	return unicode.IsLetter(rune(c)) || unicode.IsDigit(rune(c))
}
```

完整代码可以查看[这里](https://github.com/kangliqi/algorithms/blob/master/src/leetcode/palindrome.go)

