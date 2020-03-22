# Golang标准库-fmt常用函数及操作举例

## 格式化占位符

```go
type Website struct{
	Name string
}
site := Website{
		Name:"studygolang",
	}
```



### 通用占位符

| 占位符 | 说明                               | 举例                  | 输出                             |
| ------ | ---------------------------------- | --------------------- | -------------------------------- |
| %v     | 相应值的默认格式                   | `Printf("%v", site)`  | {studygolang}                    |
| %+v    | 类似%v，但输出结构体时会添加字段名 | `Printf("%+v", site)` | {Name:studygolang}               |
| %#v    | 值的Go语法表示                     | `Printf("#v", site)`  | main.Website{Name:"studygolang"} |
| %T     | 值的类型的Go语法表示               | `Printf("%T", site)`  | main.Website                     |
| %%     | 百分号                             | `Printf("%%")`        | %                                |

### 布尔占位符

| 占位符 | 说明            | 举例                 | 输出 |
| ------ | --------------- | -------------------- | ---- |
| %t     | 单次true或false | `Printf("%t", true)` | true |

### 整数占位符

| 占位符 | 说明                                       | 举例                     | 输出   |
| ------ | ------------------------------------------ | ------------------------ | ------ |
| %b     | 二进制的表示                               | `Printf("%b",5)`         | 101    |
| %c     | 相应Unicode码所表示的字符                  | `Printf("%c", 0x4E2D)`   | 中     |
| %d     | 十进制表示                                 | `Printf("%d\n", 0x12)`   | 18     |
| %o     | 八进制表示                                 | `Printf("%d\n", 10)`     | 10     |
| %q     | 单引号围绕的字符字面值，由Go语法安全地转义 | `Printf("%q\n", 0x4E2D)` | '中'   |
| %x     | 十六进制表示，字母形式为小写a-f            | `Printf("%x\n", 13)`     | d      |
| %X     | 十六进制表示，字母形式为大写A-F            | `Printf("%X\n", 13)`     | D      |
| %U     | Unicode格式：U+1234,等同于“U+%04X”         | `Printf("%U", 0x4E2D)`   | U+4E2D |

### 浮点数和复数的组成部分（实部和虚部）

| 占位符 | 说明                                                   | 举例                       | 输出         |
| ------ | ------------------------------------------------------ | -------------------------- | ------------ |
| %b     | 无小数部分、二进制指数的科学计数法，如-123456p-78;与   |                            |              |
| %e     | 科学计数法，如-12345.456e+78                           | `Printf("%e\n", 10.2)`     | 1.020000e+01 |
| %E     | 科学计数法，如-12345.456e+78                           | `Printf("%E\n", 10.2)`     | 1.020000E+01 |
| %f     | 有小数部分但无指数部分，如123.456                      | `Printf("%f\n", 10.2)`     | 10.200000    |
| %F     | 等价于%f                                               | `Printf("%F\n", 10.2)`     | 10.200000    |
| %g     | 根据实际情况采用%e或%f格式（以获得更简洁、准确的输出） | `Printf("%g\n", 10.20)`    | 10.2         |
| %G     | 根据实际情况采用%E或%F格式（以获得更简洁、准确的输出） | `Printf("%G\n", 10.20+2i)` | (10.2+2i)    |

### 字符串与字节切片

| 占位符 | 说明                                                         | 举例                                   | 输出           |
| ------ | ------------------------------------------------------------ | -------------------------------------- | -------------- |
| %s     | 直接输出字符串或者字节切片                                   | `Printf("%s", []byte("Go语言中文网"))` | Go语言中文网   |
| %q     | 该值对应的双引号括起来的go语法字符串字面值，必要时会采用安全的转义表示 | `Printf("%q", "Go语言中文网")`         | "Go语言中文网" |
| %x     | 每个字节用两字符十六进制数表示（使用a-f）                    | `Printf("%x", "golang") `              | 676f6c616e67   |
| %X     | 每个字节用两字符十六进制数表示（使用A-F）                    | `Printf("%X", "golang") `              | 676F6C616E67   |

### 指针

| 占位符 | 说明                         | 举例                   | 输出         |
| ------ | ---------------------------- | ---------------------- | ------------ |
| %p     | 表示为十六进制，并加上前导0x | `Printf("%p", &site) ` | 0xc0420381c0 |



### type [Stringer](https://github.com/golang/go/blob/master/src/fmt/print.go?name=release#63)

```go
type Stringer interface {
    String() string
}
```

实现了Stringer接口的类型（即有String方法），定义了该类型值的原始显示。当采用任何接受字符的verb（%v %s %q %x %X）动作格式化一个操作数时，或者被不使用格式字符串如Print函数打印操作数时，会调用String方法来生成输出的文本。

### type [GoStringer](https://github.com/golang/go/blob/master/src/fmt/print.go?name=release#71)

```go
type GoStringer interface {
    GoString() string
}
```

实现了GoStringer接口的类型（即有GoString方法），定义了该类型值的go语法表示。当采用verb %#v格式化一个操作数时，会调用GoString方法来生成输出的文本。

### type [State](https://github.com/golang/go/blob/master/src/fmt/print.go?name=release#39)

```go
type State interface {
    // Write方法用来写入格式化的文本
    Write(b []byte) (ret int, err error)
    // Width返回宽度值，及其是否被设置
    Width() (wid int, ok bool)
    // Precision返回精度值，及其是否被设置
    Precision() (prec int, ok bool)
    // Flag报告是否设置了flag c（一个字符，如+、-、#等）
    Flag(c int) bool
}
```

State代表一个传递给自定义Formatter接口的Format方法的打印环境。它实现了io.Writer接口用来写入格式化的文本，还提供了该操作数的格式字符串指定的选项和宽度、精度信息（通过调用方法）。

没有%u。整数如果是无符号类型自然输出也是无符号的。类似的，也没有必要指定操作数的尺寸（int8，int64）。

宽度通过一个紧跟在百分号后面的十进制数指定，如果未指定宽度，则表示值时除必需之外不作填充。精度通过（可选的）宽度后跟点号后跟的十进制数指定。如果未指定精度，会使用默认精度；如果点号后没有跟数字，表示精度为0。举例如下：

```go
%f:    默认宽度，默认精度
%9f    宽度9，默认精度
%.2f   默认宽度，精度2
%9.2f  宽度9，精度2
%9.f   宽度9，精度0    
```

宽度和精度格式化控制的是Unicode码值的数量（不同于C的printf，它的这两个因数指的是字节的数量）。两者任一个或两个都可以使用'*'号取代，此时它们的值将被对应的参数（按'*'号和verb出现的顺序，即控制其值的参数会出现在要表示的值前面）控制，这个操作数必须是int类型。

对于大多数类型的值，宽度是输出字符数目的最小数量，如果必要会用空格填充。对于字符串，精度是输出字符数目的最大数量，如果必要会截断字符串。

对于整数，宽度和精度都设置输出总长度。采用精度时表示右对齐并用0填充，而宽度默认表示用空格填充。

对于浮点数，宽度设置输出总长度；精度设置小数部分长度（如果有的话），除了%g和%G，此时精度设置总的数字个数。例如，对数字123.45，格式%6.2f 输出123.45；格式%.4g输出123.5。%e和%f的默认精度是6，%g的默认精度是可以将该值区分出来需要的最小数字个数。

对复数，宽度和精度会分别用于实部和虚部，结果用小括号包裹。因此%f用于1.2+3.4i输出(1.200000+3.400000i)。

其它flag：

```go
'+'	总是输出数值的正负号；对%q（%+q）会生成全部是ASCII字符的输出（通过转义）；
' '	对数值，正数前加空格而负数前加负号；
'-'	在输出右边填充空白而不是默认的左边（即从默认的右对齐切换为左对齐）；
'#'	切换格式：
  	八进制数前加0（%#o），十六进制数前加0x（%#x）或0X（%#X），指针去掉前面的0x（%#p）；
 	对%q（%#q），如果strconv.CanBackquote返回真会输出反引号括起来的未转义字符串；
 	对%U（%#U），输出Unicode格式后，如字符可打印，还会输出空格和单引号括起来的go字面值；
  	对字符串采用%x或%X时（% x或% X）会给各打印的字节之间加空格；
'0'	使用0而不是空格填充，对于数值类型会把填充的0放在正负号后面；
```

### type [Formatter](https://github.com/golang/go/blob/master/src/fmt/print.go?name=release#54)

```go
type Formatter interface {
    // c为verb，f提供verb的细节信息和Write方法用于写入生成的格式化文本
    Format(f State, c rune)
}
```

实现了Formatter接口的类型可以定制自己的格式化输出。Format方法的实现内部可以调用Sprint或Fprint等函数来生成自身的输出。

### type [ScanState](https://github.com/golang/go/blob/master/src/fmt/scan.go?name=release#29)

```go
type ScanState interface {
    // 从输入读取下一个rune（Unicode码值），在读取超过指定宽度时会返回EOF
    // 如果在Scanln、Fscanln或Sscanln中被调用，本方法会在返回第一个'\n'后再次调用时返回EOF
    ReadRune() (r rune, size int, err error)
    // UnreadRune方法让下一次调用ReadRune时返回上一次返回的rune且不移动读取位置
    UnreadRune() error
    // SkipSpace方法跳过输入中的空白，换行被视为空白
    // 在Scanln、Fscanln或Sscanln中被调用时，换行被视为EOF
    SkipSpace()
    // 方法从输入中依次读取rune并用f测试，直到f返回假；将读取的rune组织为一个[]byte切片返回。
    // 如果skipSpace参数为真，本方法会先跳过输入中的空白。
    // 如果f为nil，会使用!unicode.IsSpace(c)；就是说返回值token将为一串非空字符。
    // 换行被视为空白，在Scanln、Fscanln或Sscanln中被调用时，换行被视为EOF。
    // 返回的切片指向一个共享内存，可能被下一次调用Token方法时重写；
    // 或被使用该Scanstate的另一个Scan函数重写；或者在本次调用的Scan方法返回时重写。
    Token(skipSpace bool, f func(rune) bool) (token []byte, err error)
    // Width返回返回宽度值，及其是否被设置。单位是unicode码值。
    Width() (wid int, ok bool)
    // 因为本接口实现了ReadRune方法，Read方法永远不应被在Scanner接口中使用。
    // 一个合法的ScanStat接口实现可能会选择让本方法总是返回错误。
    Read(buf []byte) (n int, err error)
}
```

ScanState代表一个将传递给Scanner接口的Scan方法的扫描环境。 Scan函数中，可以进行一次一个rune的扫描，或者使用Token方法获得下一个token（比如空白分隔的token）。

### type [Scanner](https://github.com/golang/go/blob/master/src/fmt/scan.go?name=release#63)

```go
type Scanner interface {
    Scan(state ScanState, verb rune) error
}
```

当Scan、Scanf、Scanln或类似函数接受实现了Scanner接口的类型（其Scan方法的receiver必须是指针，该方法从输入读取该类型值的字符串表示并将结果写入receiver）作为参数时，会调用其Scan方法进行定制的扫描。

## Print系列

### func [Print](https://github.com/golang/go/blob/master/src/fmt/print.go?name=release#231)

```go
func Print(a ...interface{}) (n int, err error)
```

Print采用默认格式将其参数格式化并写入标准输出。如果两个相邻的参数都不是字符串，会在它们的输出之间添加空格。返回写入的字节数和遇到的任何错误。

### func [Printf](https://github.com/golang/go/blob/master/src/fmt/print.go?name=release#196)

```go
func Printf(format string, a ...interface{}) (n int, err error)
```

Printf根据format参数生成格式化的字符串并写入标准输出。返回写入的字节数和遇到的任何错误。

### func [Println](https://github.com/golang/go/blob/master/src/fmt/print.go?name=release#263)

```go
func Println(a ...interface{}) (n int, err error)
```

Println采用默认格式将其参数格式化并写入标准输出。总是会在相邻参数的输出之间添加空格并在输出结束后添加换行符。返回写入的字节数和遇到的任何错误。



## Fprint系列

### func [Fprint](https://github.com/golang/go/blob/master/src/fmt/print.go?name=release#220)

```go
func Fprint(w io.Writer, a ...interface{}) (n int, err error)
```

Fprint采用默认格式将其参数格式化并写入w。如果两个相邻的参数都不是字符串，会在它们的输出之间添加空格。返回写入的字节数和遇到的任何错误。

### func [Fprintf](https://github.com/golang/go/blob/master/src/fmt/print.go?name=release#186)

```go
func Fprintf(w io.Writer, format string, a ...interface{}) (n int, err error)
```

Fprintf根据format参数生成格式化的字符串并写入w。返回写入的字节数和遇到的任何错误。

### func [Fprintln](https://github.com/golang/go/blob/master/src/fmt/print.go?name=release#252)

```go
func Fprintln(w io.Writer, a ...interface{}) (n int, err error)
```

Fprintln采用默认格式将其参数格式化并写入w。总是会在相邻参数的输出之间添加空格并在输出结束后添加换行符。返回写入的字节数和遇到的任何错误。

## Sprint系列

### func [Sprint](https://github.com/golang/go/blob/master/src/fmt/print.go?name=release#237)

```go
func Sprint(a ...interface{}) string
```

Sprint采用默认格式将其参数格式化，串联所有输出生成并返回一个字符串。如果两个相邻的参数都不是字符串，会在它们的输出之间添加空格。

### func [Sprintf](https://github.com/golang/go/blob/master/src/fmt/print.go?name=release#201)

```go
func Sprintf(format string, a ...interface{}) string
```

Sprintf根据format参数生成格式化的字符串并返回该字符串。

### func [Sprintln](https://github.com/golang/go/blob/master/src/fmt/print.go?name=release#269)

```go
func Sprintln(a ...interface{}) string
```

Sprintln采用默认格式将其参数格式化，串联所有输出生成并返回一个字符串。总是会在相邻参数的输出之间添加空格并在输出结束后添加换行符。

### func [Errorf](https://github.com/golang/go/blob/master/src/fmt/print.go?name=release#211)

```go
func Errorf(format string, a ...interface{}) error
```

Errorf根据format参数生成格式化字符串并返回一个包含该字符串的错误。

### func [Scanf](https://github.com/golang/go/blob/master/src/fmt/scan.go?name=release#84)

```go
func Scanf(format string, a ...interface{}) (n int, err error)
```

Scanf从标准输入扫描文本，根据format 参数指定的格式将成功读取的空白分隔的值保存进成功传递给本函数的参数。返回成功扫描的条目个数和遇到的任何错误。

### func [Fscanf](https://github.com/golang/go/blob/master/src/fmt/scan.go?name=release#143)

```go
func Fscanf(r io.Reader, format string, a ...interface{}) (n int, err error)
```

Fscanf从r扫描文本，根据format 参数指定的格式将成功读取的空白分隔的值保存进成功传递给本函数的参数。返回成功扫描的条目个数和遇到的任何错误。

### func [Sscanf](https://github.com/golang/go/blob/master/src/fmt/scan.go?name=release#116)

```go
func Sscanf(str string, format string, a ...interface{}) (n int, err error)
```

Sscanf从字符串str扫描文本，根据format 参数指定的格式将成功读取的空白分隔的值保存进成功传递给本函数的参数。返回成功扫描的条目个数和遇到的任何错误。

### func [Scan](https://github.com/golang/go/blob/master/src/fmt/scan.go?name=release#71)

```go
func Scan(a ...interface{}) (n int, err error)
```

Scan从标准输入扫描文本，将成功读取的空白分隔的值保存进成功传递给本函数的参数。换行视为空白。返回成功扫描的条目个数和遇到的任何错误。如果读取的条目比提供的参数少，会返回一个错误报告原因。

### func [Fscan](https://github.com/golang/go/blob/master/src/fmt/scan.go?name=release#124)

```go
func Fscan(r io.Reader, a ...interface{}) (n int, err error)
```

Fscan从r扫描文本，将成功读取的空白分隔的值保存进成功传递给本函数的参数。换行视为空白。返回成功扫描的条目个数和遇到的任何错误。如果读取的条目比提供的参数少，会返回一个错误报告原因。

### func [Sscan](https://github.com/golang/go/blob/master/src/fmt/scan.go?name=release#103)

```go
func Sscan(str string, a ...interface{}) (n int, err error)
```

Sscan从字符串str扫描文本，将成功读取的空白分隔的值保存进成功传递给本函数的参数。换行视为空白。返回成功扫描的条目个数和遇到的任何错误。如果读取的条目比提供的参数少，会返回一个错误报告原因。

### func [Scanln](https://github.com/golang/go/blob/master/src/fmt/scan.go?name=release#77)

```go
func Scanln(a ...interface{}) (n int, err error)
```

Scanln类似Scan，但会在换行时才停止扫描。最后一个条目后必须有换行或者到达结束位置。

### func [Fscanln](https://github.com/golang/go/blob/master/src/fmt/scan.go?name=release#133)

```go
func Fscanln(r io.Reader, a ...interface{}) (n int, err error)
```

Fscanln类似Fscan，但会在换行时才停止扫描。最后一个条目后必须有换行或者到达结束位置。

### func [Sscanln](https://github.com/golang/go/blob/master/src/fmt/scan.go?name=release#109)

```go
func Sscanln(str string, a ...interface{}) (n int, err error)
```

Sscanln类似Sscan，但会在换行时才停止扫描。最后一个条目后必须有换行或者到达结束位置。