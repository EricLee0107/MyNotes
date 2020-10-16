####  Go常用包之flag包



```
[07] golang
```



`func String(参数名，参数默认值，用法注释)`
用于声明参数变量
在命令行属于参数时，格式为 `-参数名 参数值`，当没有对对应参数名传入值，则使用参数默认值。

**示例：**

```go
var infile *string = flag.String("i", "unsorted.dat", "File contains values for sorting")
func Parse()
```