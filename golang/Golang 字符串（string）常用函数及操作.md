## Golang 字符串（string）系列函数功能与用法详解

### 常用函数

**ContainsAny(str, chars) bool**

如果str中包含chars中的任意一个字符，返回true，否则返回false

例子：

```go
	str := "hello world"
	fmt.Println(strings.ContainsAny(str,"bcfa"),strings.ContainsAny(str,"dcfa"))
```

输出：false true

 **ContainsRune(str,rune) bool**

str中是否包含单个unicode字符

例子：

```go
str := "hello world"
fmt.Println(strings.ContainsRune(str,'w'),strings.ContainsRune(str,'s'))
```

输出：true false

**Count(str,substr) int**

字符串中包含某字符串的次数, 没有时返回0

例子：

```go
str := "hello world"
fmt.Println(strings.Count(str, "l"), strings.Count(str, "t")) 
```

输出：3 0

**EqualFold(str,str2)**

判断两个utf-8编码字符串（将unicode大写、小写、标题三种格式字符视为相同）是否相同。

例子：

```go
fmt.Println(strings.EqualFold("Go", "go"))
```

输出：true

**Fields(str) []string**

返回将字符串按照空白（unicode.IsSpace确定，可以是一到多个连续的空白字符）分割的多个字符串。如果字符串全部是空白或者是空字符串的话，会返回空切片。

例子：

```go
str := "hello         world     hello    boy"
fmt.Println(strings.Fields(str))
```

输出：[hello world hello boy]

**FieldsFunc(str func(r rune)bool{})**

类似Fields，但使用函数f来确定分割符（满足f的unicode码值）。如果字符串全部是分隔符或者是空字符串的话，会返回空切片

例子：

isabc函数

```go
func isabc(r rune) bool{
   switch r{
   case 'a','b','c':
      return true
   default:
      return false
   }
}
```

FieldsFunc函数

```go
str := "hahbhch"
fmt.Println(strings.FieldsFunc(str,isabc))
```

输出：[h h h h]

**HasPrefix(str,prex)**

判断是否以某个字符串开头

例子：

```go
str := "hello world"
//以某个字符串开始
i := strings.HasPrefix(str, "h")
j := strings.HasPrefix(str, "t")
fmt.Println(i, j) 
```

输出：true false

**HasSuffix(str,sufx)**

判断是否以某个字符串结尾

例子：

```go
str := "hello world"
//以某个字符串结尾
i1 := strings.HasSuffix(str, "h")
j1 := strings.HasSuffix(str, "d") //是不是以d结尾的
fmt.Println(i1, j1)               
```

输出：false true

**Index(str,substr)**

获取指定内容在字符串中首次出现的位置,中文会算成 3个字符

例子：

```go
str := "hello world"
// 获取指定内容在str中首次出现的位置，如果有则返回该元素索引, 如果没有则返回-1
fmt.Println(strings.Index(str, "l"), ",", strings.Index(str, "t")) 
```

输出：2 , -1

**IndexAny(str,chars) int**

返回chars中存在的unicode字符第一次出现的位置（下标从0开始）

例子：

```go
str := "hello world"
fmt.Println(strings.IndexAny(str,"cw"))
```

输出：6

**IndexByte(str,char) int**

str中第一次char出现的位置，不存在则返回-1。 中文无法使用

例子：

```go
str := "hello world"
fmt.Println(strings.IndexByte(str,'e'))
```

输出：1

**IndexFunc(str,func(rune)bool) int**

自定义第一次查找规则

例子：

isabc函数

```go
func isabc(r rune) bool{
   switch r{
   case 'a','b','c':
      return true
   default:
      return false
   }
}
```

```go
str := "hello world"
fmt.Println(strings.IndexFunc(str,isabc))
```

因为`a`,`b`,`c`都不在`hello world`中所以返回-1

输出：-1

**IndexRune(str,r) int**

unicode码值在str中第一次出现的位置，不存在则返回-1。

例子：

```go
str := "hello world"
fmt.Println(strings.IndexRune(str,'d'))
```

输出：10

**Join([]string,sep)**

用指定字符串将slice中的所有元素链接成一个字符串

例子：

```GO
//用指定字符将 string 类型的 slice 中所有元素链接成一个字符串
str4 := []string{"a","b","c","d"}
fmt.Println(strings.Join(str4,"-"))	//用-连接str4中的所有元素
```

输出：a-b-c-d

**LastIndex(str,substr) int**

获取指定内容在字符串中最后一次出现的位置 中文会算成 3个字符

例子：

```go
str := "hello world"
// 获取指定内容在str中最后一次出现的位置, 如果有则返回该元素索引, 如果没有则返回-1
fmt.Println(strings.LastIndex(str, "l"), ",", strings.LastIndex(str, "t")) 
```

输出：9 , -1

**LastIndexAny(str,chars) int**

返回chars中存在的一個字符，最后一次出现的位置

例子：

```go
fmt.Println(strings.LastIndexAny(str, "lr"), ",", strings.LastIndexAny(str, "ot"))
```

输出：9，7

**LastIndexByte(str,char) int**

最后一次字符出现的位置 中文无法使用

例子：

```go
str := "bibox bibox"
fmt.Println(strings.LastIndexByte(str,'b'))
```

输出：8

**LastIndexFunc(str,func(rune)bool) int**

自定义最后一次查找规则

例子：

```go
str := "bibox"
fmt.Println(strings.LastIndexFunc(str,isabc))
```

输出：2

**Map(func(rune)rune,str) string**

根据自定义函数对字符串进行修改并返回

例子：

```go
toUpper := func(r rune) rune {
	switch {
     // 对所有小写字母改为大写
	case r >= 'a' && r <= 'z':
		return r - 32
	}
	return r
}
fmt.Println(strings.Map(toUpper, "Twas brillig and the slithy gopher..."))
```

输出：TWAS BRILLIG AND THE SLITHY GOPHER...

**Repeat(str,n) string**

将字符串str整体重复n次

例子：

```go
str := "hello world"
fmt.Println(strings.Repeat(str, 2))	
```

输出：hello worldhello world

**Replace(str,oldstr,newstr,n)**

替换字符串中指定内容

例子：

```go
//将str中的 hello 替换为 你好
str := "hello world"
fmt.Println(strings.Replace(str, "hello", "你好", 1))  
//最后一个参数表示如果str中有多个hello的话，只替换前n个， -1代表全部替換
```

输出：你好 world 

**Split(str,seq) []string**

跟去seq分隔str，返回字符串列表

例子：

```go
str := "hello world"
// 以w为分隔符，分隔hello world
res0 :=strings.Split(str,"w")
fmt.Println(res0)	
```

输出：[hello  orld]   

**SplitAfter(str,seq) []string**

正常切分并保留分隔符

例子：

```go
fmt.Printf("[%q]",strings.SplitAfter("hello,world", ","))
```

输出：[["hello," "world"]]   这里`,`没有被删除，而是和前一个字符串分隔在一起

**SplitN(str,sep,n) []string**

切分为n个字符串， 如果n是1不切分, 前n-1个分隔符会切分字符，最后一份不管包含多少个sep都合成一个字符串 不做切分

例子：

```go
fmt.Printf("[%q]",strings.SplitN("hello,world,world2,world3", ",",2))
```

输出：[["hello" "world,world2,world3"]]

**ToLower(str)**

小写

例子：

```go
//转小写
str1 := "HELLO world"
fmt.Println(strings.ToLower(str1))	//全体转小写 
```

输出：hello world

**ToUpper(str)**

大写

例子：

```go
//转大写
str1 := "HELLO world"
fmt.Println(strings.ToUpper(str1))	//全体转大写 
```

输出：HELLO WORLD

**Trim(str,cutset)**

删除两侧的指定字符，这里cutset是一个集合字符串 ";:,]"等等

例子：

```go
fmt.Printf("[%q]", strings.Trim(" !,,!! Achtung! Achtung! !!! ", ",! "))
```

输出：["Achtung! Achtung"]

**TrimFunc(str,func(rune)rune) string**

根据自定义函数去除两侧特定字符

例子：

```go
fmt.Printf("[%q]", strings.TrimFunc("\t\rhello\t\r", unicode.IsSpace))
```

输出：["hello"]   其实就是去除了`\t`和`\r`

unicode.IsSpace的实现：

```go
// IsSpace reports whether the rune is a space character as defined
// by Unicode's White Space property; in the Latin-1 space
// this is
//	'\t', '\n', '\v', '\f', '\r', ' ', U+0085 (NEL), U+00A0 (NBSP).
// Other definitions of spacing characters are set by category
// Z and property Pattern_White_Space.
func IsSpace(r rune) bool {
	// This property isn't the same as Z; special-case it.
	if uint32(r) <= MaxLatin1 {
		switch r {
		case '\t', '\n', '\v', '\f', '\r', ' ', 0x85, 0xA0:
			return true
		}
		return false
	}
	return isExcludingLatin(White_Space, r)
}
```

**TrimLeft(str,cutset)**

去左侧指定字符

```go
str := "ttt hello t world"
fmt.Println(strings.TrimLeft(str,"t"))
```

输出： hello t world   <font color = red>注意：hello前面有个空格</font>

可以同时去除多种不同字符

例子：

```go
str := "sstttst hello t world"
fmt.Println(strings.TrimLeft(str,"st"))
```

输出：hello t world

**TrimLeftFunc(str,func(rune)rune) string**

根据自定义函数去除字符串右侧的所有指定字符

例子：

```go
fmt.Printf("[%q]", strings.TrimLeftFunc("\t\rhello\t\r", unicode.IsSpace))
```

输出：["hello\t\r"]

**TrimPrefix(str,prex)**

删除字符串str的指定前缀prex,当前缀不是prex时返回原字符串str

例子：

```go
str := "hello world"
fmt.Println(strings.TrimPrefix(str,"hel"))
```

输出：lo world

**TrimRight(str,cutset)**

去除字符串右侧的所有指定字符

例子：

```go
str := "hello t world t"
fmt.Println(strings.TrimRight(str,"t"))
```

输出：hello t world

可以同时去除多种不同字符

例子：

```go
str := "hello t world sstttst"
fmt.Println(strings.TrimRight(str,"st"))
```

输出：hello t world

**TrimRightFunc(str,func(rune)rune) string**

根据自定义函数，去除右侧指定字符

例子：

```go
fmt.Printf("[%q]", strings.TrimRightFunc("\t\rhello\t\r", unicode.IsSpace))
```

输出：["\t\rhello"]

**TrimSpace(str)**

去掉字符串首尾的空格

例子：

```go
//去掉字符串首尾的空格
str := "   hello world  "
fmt.Println(strings.TrimSpace(str))
```

输出：hello world   	前后都没有空格

**TrimSuffix(str,sufx)**

去指定后缀

例子：

```go
str := "hello world"
fmt.Println(strings.TrimSuffix(str,"ld"))
```

输出： hello wor

### 不常用函数

**func Compare(a, b string) int**

比较返回一个按字典顺序比较两个字符串的整数。如果a == b则结果为0，如果a <b则结果为-1，如果a> b则结果为+1。 此外：仅包含与包字节对称的比较。使用内置字符串比较运算符==，<，>等通常更清晰，速度更快。 

例子：

```go
fmt.Println(strings.Compare("a","b"),strings.Compare("a","a"),strings.Compare("b","a"))
```

输出：-1 0 1

注意：使用大于等于小于同样可以得到同样的答案。

**Title(s string) string**

返回s中每个单词的首字母都改为标题格式的字符串拷贝。

BUG: Title用于划分单词的规则不能很好的处理Unicode标点符号（标点符号会前后会被认为是两个单次）

例子：

```go
fmt.Println(strings.Title("Twas brillig and the slithy gopher..."))
```

输出：Twas Brillig,And The Slithy Gopher...

**ToUpperSpecial(_case unicode.SpecialCase, s string) string**

使用_case规定的字符映射，返回将所有字母都转为对应的大写版本的拷贝。

例子：

**ToTitleSpecial(_case unicode.SpecialCase, s string) string**

使用_case规定的字符映射，返回将所有字母都转为对应的标题版本的拷贝。

例子：