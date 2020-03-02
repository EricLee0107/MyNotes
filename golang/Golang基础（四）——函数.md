---
title: Golang基础--函数

date: 2018-01-05

tags: [Golang, Go基础]

---

- go程序中至少有一个函数——main()函数
- 函数声明告诉了编译器函数的名称，函数的返回值类型，函数的参数。
- Go中函数可以返回多个值。

### 函数定义
Go 语言函数定义格式如下：
```go
func function_name( [parameter list] )[return_types]
{
   函数体
}
```
函数定义解析：

- func：函数由 func 开始声明
- function_name：函数名称，函数名和参数列表一起构成了函数签名。
- parameter list]：参数列表，参数就像一个占位符，当函数被调用时，你可以将值传递给参数，这个值被称为实际参数。参数列表指定的是参数类型、顺序、及参数个数。参数是可选的，也就是说函数也可以不包含参数。
- return_types：返回类型，函数返回一列值。return_types 是该列值的数据类型。有些功能不需要返回值，这种情况下 return_types 不是必须的。
- 函数体：函数定义的代码集合。
<!-- more-->
#### 函数的参数

参数的传递方式：
1. 值传递：在调用函数时将实际参数赋值一份传递到函数中，如果函数对参数进行修改，不会影响到实际参数。
2. 引用传递（地址传递）：在调用函数时将实际参数的地址传递到函数中，在函数中对参数进行修改，实际上修改的是指针所指向的值，会影响到实际参数。

> **默认情况下Go语言使用值传递。**


**多返回值代码实例：**

```go
package main

import "fmt"

func swp(x, y int) (int, int) {
	return y, x
}

func main() {
	a,b := 1,2
	c, d := swp(a, b)
	fmt.Println(c, d)
}
```

**值传递代码实例：**

```go
package main

import "fmt"

func swp(x, y int) (int, int) {
	temp := x
	x = y
	y = temp
	return x, y
}

func main() {
	x, y := 1, 2
	fmt.Printf("值传递前x为:%d,y为:%d\n", x, y)
	z, w := swp(x, y)
	// 值传递只是对x,y进行了拷贝，并没有改变x,y原有的值
	fmt.Printf("值传递后x为:%d,y为:%d\n", x, y)
	// 通过x,y的拷贝运算结果返回给了z,w 并没有改变x,y
	fmt.Println(z, w)
}
```

**引用传递代码实例：**
```go
package main

import "fmt"

func swp(x, y *int) (*int, *int) {
	temp := *x
	*x = *y
	*y = temp
	return x, y
}

func main() {
	x, y := 1, 2
	fmt.Printf("指针传递前x为:%d,y为:%d\n", x, y)
	z, w := swp(&x, &y)
	// 指针传递实际上在交换时交换了指针所指向地址的值，所以x,y发生了变化
	fmt.Printf("指针传递后x为:%d,y为:%d\n", x, y)
	fmt.Println(*z, *w)
}
```

#### 函数用法

**函数作为值**
Go语言中函数可以作为值赋值给变量，同时函数可以作为参数和返回值。
**方法**
Go中同时有函数和方法，一个方法就是一个包含了接受者的函数，接受者可以是命名类型或者结构体类型的一个值或者一个指针。所有给定类型的方法属于该类型的方法集



