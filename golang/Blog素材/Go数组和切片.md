# 数组

数组是一个由固定长度的特定类型元素组成的序列。数组的长度是数组类型的一个组成部分，不同长度的数组即使内部元素类型相同也不属于同一个数组类型。

在Go中很少直接使用数组，一般使用slice(切片)来代替数组。

### 数组的声明与初始化

#### 数组的声明方式

```go
// 声明一个数组
[length]Type
// 声明并初始化一个数组
[N]Type{value1,value2,...,valueN}
// 声明并初始化一个数组，数组长度在编译时自动计算
[]Type{value1,value2,...,valueN}
```

数组的长度必须是一个常量或常量表达式，因为在编译时需要确定数组的长度。

数组可以不初始化或者只进行部分初始化，未被初始化的部分，则会自动初始化为对应类型的零值。

#### 一维数组

```go
var a [4]int //元素自动初始化为零[0 0 0 0] 
b := [4]int{2, 5} //未提供初始化值得元素自动初始化为0  [2 5 0 0] 
c := [...]int{1, 2, 3} //编译器按初始化值数量确定数组长度 [1 2 3] 
d := [2]string{"TigerwolfC","chen_peggy"}
e := [...]int{10, 3: 100} //支持索引初始化，但注意数组长度与此有关 [10 0 0 100]
f := [4]string{1: "b", 3: "c"}  // 可以指定初始化的位置
```
#### 复合类型数组

```go

type User struct {
	Name string 
	Age byte 
} 
d := [...]user{
     {"TigerwolfC", 20},// 可省略元素类型。 
     {"chen_peggy", 18},// 别忘了最后一行的逗号。
   }
```
### 数组的比较

**数组比较的前提**：

1. 数组内的数据类型是可比较的；
2. 两个数组的长度相同，因为数组长度也是数组的一部分，不同长度的数组进行比较会引发编译错误；

*只有当两个数组中的所有元素都相等是才相等*

### 数组作为参数传递

当函数调用需要传递一个数组时，最好不要直接传递数组本身，因为函数的每个调用参数将会被赋值给函数内部的参数变量（无论是数组本身还是指向数组的指针），所以函数参数变量接收的是传入参数的拷贝，在函数内对数组参数的任何修改都是发生在拷贝的数组上的，并不回修改原始的数组变量。

另外，传递一个很大的数组类型是非常低晓得（拷贝数据较大），所以可以改为使用指针开传递数组,这样拷贝的时指针，对指针指向的数组的数据进行修改，会直接修改到原始数组，而且传递也是高效的（只需要拷贝一个指针）。

### 数组常用操作

#### 数组去重

方法一

```go
func ArrayRemoveRepeated(arr []string) []string {
	sort.Strings(arr)
	i:= 0
	var j int
	for{
		if i >= len(arr)-1 {
			break
		}
		for j = i + 1; j < len(arr) && arr[i] == arr[j]; j++ {
		}
		arr= append(arr[:i+1], arr[j:]...)
		i++
	}
	return  arr
}
```
方法二

```go
func ArrayRemoveRepeated(arr []string) (newArr []string) {
	newArr = make([]string, 0)
	for i := 0; i < len(arr); i++ {
		repeat := false
		for j := i + 1; j < len(arr); j++ {
			if arr[i] == arr[j] {
				repeat = true
				break
			}
		}
		if !repeat {
			newArr = append(newArr, arr[i])
		}
	}
	return
}
```
### 删除数组中某个元素

删除第i个元素

```go
a := []int{0, 1, 2, 3, 4}
//删除第i个元素
i := 2
a = append(a[:i], a[i+1:]...)
```

通过索引删除

```go
func remove(s []string, i int) []string {
	return append(s[:i], s[i+1:]...)
}
```
其他方法删除某个元素

```go
func Remove(slice []interface{}, elem interface{}) []interface{}{
	if len(slice) == 0 {
		return slice
	}
	for i, v := range slice {
		if v == elem {
			slice = append(slice[:i], slice[i+1:]...)
			return remove(slice,elem)
			break
		}
	}
	return slice
}

func RemoveZero(slice []interface{}) []interface{}{
	if len(slice) == 0 {
		return slice
	}
	for i, v := range slice {
		if ifZero(v) {
			slice = append(slice[:i], slice[i+1:]...)
			return removeZero(slice)
			break
		}
	}
	return slice
}
```


## 切片

#### 切片的创建
```go
	make([]Type,len,cap)
	make([]Type,len)
	[]Type{}
	[]Type{val1,val2,...,valN}
```







```go
	a := make([]int32, 10)
	for i := 0; i < 10; i++ {
		a[i] = int32(i + 10)
	}
	b := a[3:]
	b[3] = 33
	fmt.Println(a)
	fmt.Println(b)
```

输出：

```
[10 11 12 13 14 15 33 17 18 19]
[13 14 15 33 17 18 19]
```

在改变b[3]时也改变了a[6]原因是两个值的指针相同





翻转切片

```go
func reverse(s []int) {
	for i, j := 0, len(s)-1; i < j; i, j = i+1, j-1 {
		s[i], s[j] = s[j], s[i]
	}
}
```



切片循环向左旋转n个元素

```go
s := []int{0, 1, 2, 3, 4, 5}
// Rotate s left by two positions.
reverse(s[:2])
reverse(s[2:])
reverse(s)
fmt.Println(s) // "[2 3 4 5 0 1]"
```



append 

内置append实现策略





切片不支持`==`和`!=`操作