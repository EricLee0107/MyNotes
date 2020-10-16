### Golang基础——条件语句

> 判断语句不需要加（）
> 必须加大括号，且左大括号必须与表达式同行

#### if语句

**语法：**

```
if 布尔表达式 {
    当表达式为true时，执行的语句
}
```

**流程图：**

Start判断条件是否为真?条件为真执行的操作Endyesno

**代码实例**

```
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

Start判断条件是否为真?条件为真执行的操作End条件为假时执行的操作yesno

**代码实例**

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

Start表达式1是否为真？表达式1为真时执行的操作End表达式2是否为真？表达式2为真时执行的操作其他情况执行的操作yesnoyesno

**代码实例**

> 在if之后，条件语句之前，可以添加变量初始化语句，使用`；`分割。
> 如果函数又返回值，最终的return语句不允许包含在if…else…中，会导致编译错误。`function ends without a return statement`

错误演示：

```golang
func example(x int) int {
    if x == 0 {
        return 5
    } else {
    return x
    }
}
```

正确做法(未完成)：
``

 

 821

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

[**[07\] golang**go常用包之strconv包](javascript:void(0))

归档

[ [07\] golang](javascript:void(0))

- [Golang基础——条件语句](javascript:void(0))
- [go常用包之io包](javascript:void(0))

[ （默认笔记本）](javascript:void(0))