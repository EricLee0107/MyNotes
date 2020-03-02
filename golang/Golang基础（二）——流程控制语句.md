---
title: Golang基础--流程控制语句

date: 2018-01-05

tags: [Golang, Go基础]

---

#### for循环语句
1. go只有for关键字，没有while和do-while
2. for后面的条件表达式不需要用圆括号`()`括起来。
3. 左花括号与for同行
#### 判断语句
判断语句不需要加（）
必须加大括号，且左大括号必须与表达式同行
**if语句**
<!-- more-->
**语法：**
```
if 布尔表达式 {
	当表达式为true时，执行的语句
}
```
**流程图：**
![if](https://eric-typora-img.oss-cn-beijing.aliyuncs.com/typora/20200302224152-912540.png)
**代码实例**

```go
package main

import "fmt"

func main() {
	a := 10
	if a < 100 {

		fmt.Println(a, "小于100")
	}
}
```
输出：10小于100

#### if-else语句
**语法**
```
if 布尔表达式{
   /* 在布尔表达式为 true 时执行 */
}
else{
  /* 在布尔表达式为 false 时执行 */
}
```
**流程图**
![if-else](https://eric-typora-img.oss-cn-beijing.aliyuncs.com/typora/20200302224156-996441.png)

#### if-else if-else多重判断

 **语法**
```
if 布尔表达式1{
   /* 在布尔表达式1为 true 时执行 */
}
esle if 布尔表达式2{
	/* 在布尔表达式2为 true 时执行 */
}
else{
  /* 在布尔表达式为 false 时执行 */
}
```
**流程图**

![image-20200302224245425](https://eric-typora-img.oss-cn-beijing.aliyuncs.com/typora/20200302224247-464427.png)

> 在if之后，条件语句之前，可以添加变量初始化语句，使用`；`分割。
> 如果函数有返回值，最终的return语句不允许包含在if...else...中，会导致编译错误。`function ends without a return statement`

错误演示：
```go
func example(x int) int {
	if x > 0 {
		return 5
	} else {
	return x
	}
}
```

正确做法

```go
func example(x int) int {
      if x > 0 {
          return 5
      } else {
          fmt.Printf("%d ", x)
      }
      return x
  }

```

#### switch语句

**语法**
```
switch var1 {
    case val1:
        ...
    case val2:
        ...
    default:
        ...
}
```

