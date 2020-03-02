---
title: Golang基础--函数二

date: 2018-01-05

tags: [Golang, Go基础]

---

#### 匿名函数
匿名函数就是将一个函数直接赋值给一个变量，这个函数没有函数名，所以叫匿名函数。

##### 代码实例
```go
package main

import "fmt"

func main() {
	// 这里定义了一个匿名函数，且赋值给a
	a := func(i int, j int) (int, int) {
		return j, i
	}
	fmt.Println(a(3,4))

}
```
<!-- more-->
#### 闭包
Go的闭包： 可以简单理解为一个函数返回了一个匿名函数。

##### 代码实例
```go
package main

import "fmt"

// 定义一个闭包函数
func a(i int) func(x int) int {
	return func(x int) int {
		return i + x
	}
}

func main() {
	// 将a(10)赋值给num，即num := func(x int) int{return 10+x}
	num := a(10)
	//这里每次都是做10+x的计算
	fmt.Println(num(1))
	fmt.Println(num(2))
	fmt.Println(num(3))
}
```
运行结果：
```
11
12
13
```


#### defer（延迟函数）
defer语句时一种延迟执行的语句，在函数中可以添加多个defer语句，当函数执行到最后时，会逆序执行defer 语句操作。可以理解为在函数执行时遇到defer语句先将语句放入一个堆栈中，当函数执行结束时从堆栈中一条条取出defer语句执行。

defer 主要用于资源的管理和回收。
无论发生任何错误已经defer的语句都会照常执行。

##### 代码实例
```go
package main

import "fmt"
import "os"

func main() {
	f := createFile("tmp/defer.txt")
	// 在打开文件的位置调用defer函数，保证文件一定会被关闭
	defer closeFile(f)
	writeFile(f)
}
func createFile(p string) *os.File {
	fmt.Println("creating")
	f, err := os.Create(p)
	// 这里用到了panic函数，如果在打开文件时发生错误就会中断函数
	if err != nil {
		panic(err)
	}
	return f
}
func writeFile(f *os.File) {
	fmt.Println("writing")
	fmt.Fprintln(f, "data")
}
func closeFile(f *os.File) {
	fmt.Println("closeing")
	f.Close()
}
```

#### Panic函数
Go的一个异常处理函数，可以中断函数的控制流程，让函数进入“恐慌"状态。但函数中的延迟函数还是会正常执行（前提是处于panic之前的延迟函数）。

##### 代码实例
```go
package main

import "fmt"

func main() {
	a()
	b()
	c()
}

func c() {
	fmt.Println("func c")
}
func b() {
	panic("panic in b")
}
func a() {
	fmt.Println("func a")
}
```

#### Recover函数

与Panic相对应的函数，可以将处于恐慌的goroutine恢复，**recover函数只有在延迟函数中才可以正常生效**，在函数正常执行的过程中，调用recover函数回返回nil，但如果当前goroutine陷入恐慌，调用recover可以捕获到panic的函数值，并且恢复后续正常的执行。

##### 代码实例
```go
package main

import "fmt"

func main() {
	a()
	b()
	c()
}

func c() {
	fmt.Println("func c")
}
func b() {
	defer func() {
		if err := recover(); err != nil {
			fmt.Println("Recover in B")
		}
	}()
	panic("panic in b")
}
func a() {
	fmt.Println("func a")
}
```
