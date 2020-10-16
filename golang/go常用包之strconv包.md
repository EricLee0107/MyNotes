#### go常用包之strconv包

#### atob.go

```
func ParseBool(str string) (bool,error)
```

将字符串转为布尔值
只接受`1,t,T,TRUE,true,True,0,f,F,FALSE,false,False` 其他任何值都返回Err

**示例：**

```go
package main

import "strconv"
import "fmt"

func main() {
    b, e := strconv.ParseBool("0")
    fmt.Println(b, e)
    b, e = strconv.ParseBool("1")
    fmt.Println(b, e)
    b, e = strconv.ParseBool("f")
    fmt.Println(b, e)
    b, e = strconv.ParseBool("t")
    fmt.Println(b, e)
    b, e = strconv.ParseBool("F")
    fmt.Println(b, e)
    b, e = strconv.ParseBool("T")
    fmt.Println(b, e)
    b, e = strconv.ParseBool("false")
    fmt.Println(b, e)
    b, e = strconv.ParseBool("true")
    fmt.Println(b, e)
    b, e = strconv.ParseBool("FALSE")
    fmt.Println(b, e)
    b, e = strconv.ParseBool("TRUE")
    fmt.Println(b, e)
    b, e = strconv.ParseBool("True")
    fmt.Println(b, e)
    b, e = strconv.ParseBool("False")
    fmt.Println(b, e)
    b, e = strconv.ParseBool("")
    fmt.Println(b, e)
    b, e = strconv.ParseBool("asdf")
    fmt.Println(b, e)
}
```

结果：

```
false <nil>
true <nil>
false <nil>
true <nil>
false <nil>
true <nil>
false <nil>
true <nil>
false <nil>
true <nil>
true <nil>
false <nil>
false strconv.ParseBool: parsing "": invalid syntax
false strconv.ParseBool: parsing "asdf": invalid syntax
func FormatBool(b bool)string
```

将布尔值转为字符串“true”或“false”
只可以传入true/false

**示例：**

```go
package main

import "strconv"
import "fmt"

func main() {

    b := strconv.FormatBool(true)
    fmt.Println(b)
    b = strconv.FormatBool(false)
    fmt.Println(b)
}
```

结果：

```
true
false
func AppendBool(dst []byte, b bool) []byte
```

将转化为的字符串追加到dst尾部，返回新的dst（即将转换完的字符串拼接到字符串尾部）

#### atof.go

```
func ParseFloat(s string, bitSize int) (float64, error)
```

将字符串转换为浮点数,bitSize用于指定浮点数类型`32`代表`float32`,`64`代表`float64`
返回值为四舍五入（IEEE754标准）
s格式不正确，则返回`ErrSyntax`
如果转换结果超出 bitSize 范围，则返回`ErrRange`

#### atoi.go

```
func ParseInt(s string, base int, bitSize int)(i int64, err error)
```

将字符串解析为整数，支持正负号，base 表示进制（2到64 或者 0），0代表根据字符串前缀判断进制。
0x：16进制
0：8进制
不写前缀代表十进制
bitSize 表示位数 32位或64位， 0表示最大位宽

```
func Atoi(s string) (int, error)
```

将字符串转换为十进制整数

#### ftoa.go

```
func FormatFloat(f float64, fmt byte, prec, bitSize int) string
```

将浮点数转换为字符串形式，
`f`:要转换的浮点数
`fmt`：格式标记（b,e,E,f,g,G）
`bitSize`：指定浮点数类型
如果格式标记为`e`、`E`、`f`则prec表示小数点后的数字位数
如果格式标记为`G`、`g`则prec表示总的数字位数（小数位数+整数位数）

`func AppendFloat(dst []byte, f float64, fmt byte, prec, bitSize int) []byte`
将浮点型转换为字符串追加在dst后，返回新的dst

#### itoa.go

```
func FormatUint(i uint64, base int) string
```

将整数转换为字符串，base表示转换进制，取值范围2到36。结果中大于10的数字用小写字母a-z表示。

```
func FormatInt(i int64, base int) string
```

同FormatUnit

```
func Itoa(i int) string
```

将整数转换为十进制字符串形式FormatInt(i,10)

```
func AppendInt(dst []byte, i int64, base int) []byte
```

将整数转换为字符串追加在dst后，返回追加后的dst

```
func AppendUint(dst []byte, i uint64, base int) []byte
```

将无符号整数转换为字符串追加在dst后，返回追加后的dst

 

