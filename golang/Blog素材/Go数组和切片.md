切片不支持`==`和`!=`操作

### 数组初始化

#### 数组的创建

```go
[length]Type
[N]Type{value1,value2,...,valueN}
[]Type{value1,value2,...,valueN} //自动计算长度
```









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
### 数组去重

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

