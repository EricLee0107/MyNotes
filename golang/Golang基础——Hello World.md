---
title: Golang基础--Hello World

date: 2018-01-05

tags: [Golang, Go基础]

---

```go
package main

import "fmt"

func main() {
	// print hello world
	hw := "hello world"
    fmt.Println(hw)
 }
```
go程序主要包含下面几个部分：
	- 包声明：`package main`
	- 引入包 ：`import "fmt"`
	- 函数：`func main()`
	- 注释：`//print hello...`
	- 变量：`hw`
	- 语句&表达式：`fmt.PrintLn(HelloWorld)`
<!-- more-->
1. 第一行代码 package main定义了包名，包名必须在非注释的第一行指明这个文件所属的包。 package main表示是一个可独立执行的程序，main包是每个应用程序必须包含的包。
2. 第三行代码 import "fmt" 引入了一个标准库，golang中引用的包必须被应用不然会报错。
3. 第五行代码 func main() 是程序的入口。main函数是每一个可执行程序程序所必须包含的。
4. 第六行代码 是一个注释，go的注释方法和c/c++一样，单行注释使用 `//`，块（多行）注释使用`/**/`。
5. 第七行中hw是一个变量，在go中如果当标识符（包括变量、常量、函数名、结构体字段、类型等）以大写字母开头则代表使用这种形式的表示符的对象可以被外部包的代码使用；如果以小写字母开头则对包外不可见。
6. 第八行，调用fmt库的Println函数打印hw变量。





