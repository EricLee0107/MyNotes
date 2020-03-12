## Golang 字符串（string）常用函数及操作





**Count(str,substr) int**

字符串中包含某字符串的次数, 没有时返回0

例子：

```go
str := "hello world"
fmt.Println(strings.Count(str, "l"), strings.Count(str, "t")) 
```

输出：3 0

**ContainsAny(str, chars) bool**

包含任何Unicode字符

例子：

 **ContainsRune(str,rune) bool**

包含unicode单个字符

例子：

**Count(str,substr) int**

统计子串出现的次数

例子：

**EqualFold(str,t)**

对是否所有的unicode字符都相等

例子：

**Fields(str) []string**

根据空格分割所有

例子：

**FieldsFunc(str func(r rune)bool{})**

根据指定规则拆分字符串

例子：



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

返回任何unicode字符(\r\n)第一次出现的位置

例子：

**IndexByte(str,char) int**

第一次字符出现的位置 中文无法使用

例子：

**IndexFunc(str,func(rune)bool) int**

自定义第一次查找规则

例子：

**IndexRune(str,rune) int**

第一次unicode字符出现的位置

例子：

**Join([]string,sep)**

用指定字符串将slice中的所有元素链接成一个字符串

```GO
//用指定字符将 string 类型的 slice 中所有元素链接成一个字符串
str4 := []string{"a","b","c","d"}
fmt.Println(strings.Join(str4,"-"))	//用-连接str4中的所有元素
```

输出：a-b-c-d

例子：

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

返回任何unicode字符(\r\n)最后一次出现的位置

例子：

**LastIndexByte(str,char) int**

最后一次字符出现的位置 中文无法使用

例子：

**LastIndexFunc(str,func(rune)bool) int**

自定义最后一次查找规则

例子：

**Map(func(rune)rune,str) string**

根据自定义函数对字符串进行修改并返回

例子：

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
//最后一个参数表示如果str中有多个hello的话，只替换前n个
```

输出：你好 world 

**ReplaceAll(str,oldstr,newstr)**

替换全部

例子：

**Split(str,seq) []string**

正常切分

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

**SplitAfterN(str,sep,n) []string**

切分保留分割符

例子：

**SplitN(str,sep,n) []string**

n是切分成几个string 如果是 1不切分 n 前n-1切分 最后一份不管包含多少个sep 都合成一个字符串 不做切分

例子：

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

这里cutset是一个集合字符串 ";:,]"等等

例子：

**TrimFunc(str,func(rune)rune) string**

两侧去指定字符

例子：

**TrimLeft(str,cutset)**

去左侧指定字符

```
str2 := "  hello world tt"

//去掉字符串尾指定的字符
fmt.Println(strings.TrimRight(str2,"t"))	//  hello world 字符串首时为TrimLeft()

//去掉字符串首尾的空格
fmt.Println(strings.TrimSpace(str2))	//hello world

//去掉字符串首尾指定的字符
fmt.Println(strings.Trim(str2,"t"))		//  hello world
fmt.Println(strings.Trim(str2,"ttt"))	//注意相同的字母即时数量比str的多也能去掉  hello world
fmt.Println(strings.Trim(str2,"  "))	//去除首尾空格hello world tt
fmt.Println(strings.Trim(str2,"b"))		//没有b时不报错返回原字符串  hello world tt
```



例子：

**TrimLeftFunc(str,func(rune)rune) string**

去除字符串右侧的所有指定字符

例子：

```go
str := "ttt hello t world"
fmt.Println(strings.TrimRight(str,"t"))
```

输出： hello t world   <font color = red>注意：hello前面有个空格</font>

可以同时去除多种不同字符

例子：

```go
str := "sstttst hello t world"
fmt.Println(strings.TrimRight(str,"st"))
```

输出：hello t world

**TrimPrefix(str,prex)**

去指定前缀

例子：

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

右侧去指定字符

例子：

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

例子：

例子：

例子：

例子：



原文链接：https://blog.csdn.net/wujiangwei567/article/details/90488366



