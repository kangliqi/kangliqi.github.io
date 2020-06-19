# Leet Code 解题

- [Leet Code 解题](#leet-code-解题)
	- [125. 验证回文串](#125-验证回文串)
	- [394. 字符串解码](#394-字符串解码)

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

## 394. 字符串解码

给定一个经过编码的字符串，返回它解码后的字符串。

编码规则为: `k[encoded_string]`，表示其中方括号内部的 encoded_string 正好重复 k 次。注意 k 保证为正整数。

你可以认为输入字符串总是有效的；输入字符串中没有额外的空格，且输入的方括号总是符合格式要求的。

此外，你可以认为原始数据不包含数字，所有的数字只表示重复的次数 k ，例如不会出现像 3a 或 2[4] 的输入。

示例1：
```
输入：s = "3[a]2[bc]"
输出："aaabcbc"
```

示例2：
```
输入：s = "3[a2[c]]"
输出："accaccacc"
```

示例3：
```
输入：s = "2[abc]3[cd]ef"
输出："abcabccdcdcdef"
```

示例4：
```
输入：s = "abc3[cd]xyz"
输出："abccdcdcdxyz"
```

**解题思路**：这是一道经典的可以用栈来解决的问题，这里使用了一个栈比较巧妙的解决了这个问题，当然也可以使用两个栈

- 如果当前的字符为数位，解析出一个数字（连续的多个数位）并进栈
- 如果当前的字符为字母或者左括号，直接进栈
- 如果当前的字符为右括号，开始出栈，一直到左括号出栈，出栈序列反转后拼接成一个字符串，此时取出栈顶的数字，就是这个字符串应该出现的次数，我们根据这个次数和字符串构造出新的字符串并进栈

```go
func decodeString(s string) string {
	stack := []string{}
	i, j := 0, 0

	for i < len(s) {
		if unicode.IsDigit(rune(s[i])) {
			j = i
			i++

			// 可能出现多个数字的情况，此时获取全部数字并压栈
			for unicode.IsDigit(rune(s[i])) {
				i++
			}
			stack = append(stack, string(s[j:i]))
		} else if s[i] == '[' || unicode.IsLetter(rune(s[i])) {
			// 字母和 '[' 直接压栈
			stack = append(stack, string(s[i]))
			i++
		} else {
			str := ""
			// 如果是 ']'，从栈中取元素，拼接成字符串，直到遇到了 '['
			for len(stack) > 0 && stack[len(stack)-1] != "[" {
				str = stack[len(stack)-1] + str
				stack = stack[0 : len(stack)-1]
			}
			stack = stack[0 : len(stack)-1]

			// 前面取完了 '[' 之后的所有字符，此时栈中第一个就是数字
			rep, _ := strconv.Atoi(stack[len(stack)-1])
			stack = stack[0 : len(stack)-1]

			str = strings.Repeat(str, rep)
			stack = append(stack, str)
			i++
		}
	}

	result := ""
	for i = 0; i < len(stack); i++ {
		result += stack[i]
	}
	return result
}
```