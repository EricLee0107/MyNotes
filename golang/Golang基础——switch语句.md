### Golang基础——switch语句

#### switch 语句

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

**switch注意事项**

- 左花括号必须与switch同一行
- 条件表达式不限制未常量或者整数
- 单个case中，可以出现多个结果选项`此处如何出现多个选项结果？`
- 不需要break来明确退出一个case
- case中添加`fallthrough`关键字，会继续执行紧跟的下一个case
- 如果switch之后没有条件表达式，则switch结构与if…else…的逻辑作用等同。

变量var1可以为任意类型的变量，但val1和val必须是同类型的值（任意值）。
**代码实例**

```go
package main

import "fmt"

func main() {
    score := 89
    switch {
    case score >= 90:
        fmt.Printf("优秀")
    case score >= 80:
        fmt.Printf("良好")
    case score >= 60:
        fmt.Printf("及格")
    default:
        fmt.Printf("差")
    }
}
```

#### Type Switch(需进一步整理细节)

switch 语句还可以被用于 type-switch 来判断某个 interface 变量中实际存储的变量类型。

```
package main

import "fmt"

func main() {
    var x interface{}
    switch i := x.(type) {
    case nil:
        fmt.Printf("x的类型是：%T", i)
    case int:
        fmt.Printf("x 是int型")
    case float64:
        fmt.Printf("x是float64型")
    case string:
        fmt.Printf("x是string型")
    default:
        fmt.Printf("未知型")
    }
}
```

 

 866

260086555

[ 退出账号](javascript:void(0))

当前文档

[ 恢复至上次同步状态](javascript:void(0))[ 删除文档](javascript:void(0))[ 导出...](javascript:void(0))[ 预览文档](javascript:void(0))[ 分享链接](javascript:void(0))

系统

- [ 设置](https://maxiang.io/note/#)
- [ 下载桌面客户端](https://maxiang.io/client_zh)
- [ 下载离线Chrome App](https://chrome.google.com/webstore/detail/kidnkfckhbdkfgbicccmdggmpgogehop)
- [ 使用说明](https://maxiang.io/note/#)
- [ 快捷帮助](https://maxiang.io/note/#)
- [ 常见问题](https://www.evernote.com/shard/s21/sh/74d9e61d-2e12-4ee9-9d67-882f5d9474a4/8e6e04ad336df52156c5bf6965d8568f)
- [ 关于](https://maxiang.io/note/#)

[**[07\] golang**go常用包之bufio包](javascript:void(0))

[**[07\] golang**Golang基础——switch语句](javascript:void(0))

[**[07\] golang**Go常用包之flag包](javascript:void(0))

[不错的素材](javascript:void(0))

归档

[ （默认笔记本）](javascript:void(0))