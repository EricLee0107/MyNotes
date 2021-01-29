# go mod

## 范式

`go mod <command> [arguments]`

## command命令

### download

**范式：** `go download [-x] [-json] [modules]`

**作用：**下载指定名字的模块，可为选择主模块依赖的模块匹配模式，或path@version形式的模块查询。如果download不带参数则代表英语与是主模块的所有依赖。

go命令将在常规执行（编译、调试等等）期间根据需要自动下载模块。`go mod download`的作用主要是预填充本地缓存或者计算Go模块代理的结果。

默认情况下`go mod download` 不向标准输出写入内容(可能会打印进度信息和错误日志)。

**标志：**

- `-json`：打印一系列JSON对象至标准输出，描述每个下载的模块。
- `-x`：打印实际需要执行的命令，并运行。

### edit

### graph

**范式：**`go mod graph`

**作用：**以文本形式打印模块间的依赖关系图。输出的每一行行有两个字段（通过空格分割）：模块和其所有依赖中的一个。每个模块都被标记为path@version形式的字符串（除了主模块，因其没有@version后缀）。

### init

**范式：** `go mod init [module]`

**作用：**初始化并写入一个新的go.mod至当前目录中，实际上是创建一个以当前目录为根的新模块。文件go.mod必须不存在。如果可能，init会从import注释（参阅“go help importpath”）或从版本控制配置猜测模块路径。要覆盖此猜测，提供模块路径作为参数 `module`为当前项目名

### tidy

**范式：**`go mod tidy [-v]`

**作用：**确保go.mod与模块中的源代码一致。它添加构建当前模块的包和依赖所必须的任何缺少的模块，删除不提供任何有价值的包的未使用的模块。它也会添加任何缺少的条目至go.mod并删除任何不需要的条目。

**标志**：

- `-v`：打印被删除的模块的信息至标准错误输出。

### vendor

**范式：**`go mod vendor [-v]`

**作用：**重置主模块的vendor目录，使其包含构建和测试所有主模块的包所需要的所有包。不包括vendor中的包的测试代码。

**标志：**

- `-v`：打印vendor的模块和包的名字至标准错误输出。

### verify

**范式：**`go mod verify`

**作用：**检查存储在本地下载源代码缓存中的当前模块的依赖，是否自从下载之后未被修改。如果所有模块都未被修改，打印“all modules verified”。否则，报告哪个模块已经被修改并令“go mod”以非0状态退出。

### why

**范式：**`go mod why [-m] [-vendor] packages...`

**作用：**输出每个包或者模块的引用块，每个块以注释行“# package”或“# module”开头，给出目标包或模块。随后的行通过导入图给出路径，一个包一行。每个块之间通过一个空行分割，如果包或模块没有被主模块引用，该小节将显示单独一个带圆括号的提示信息来表明该事实。

**标志：**

- `-m`：默认情况下，why在导入图中展示从主模块到每个列出的包的最短路径。如果给出-m标志，why将参数视为一个模块的列表并找出每个模块中的所有包的路径。
- `-vendor`：默认情况下，why查询与“go list all”匹配的包图，包括对可达包的测试相关的包。-vendor标志令why将依赖包的测试相关的包排除在外。



## go.mod文件

​		模块版本是由源文件树定义的，在其根目录中有一个go.mod文件。当go命令运行时，它查找当前目录然后查找相继的父目录来找出go.mod，go.mod标记主模块的根。

go.mod文件自身是面向行的，带有`//`注释但没有`/**/`注释。每行包含单个指令，有一个动词后跟参数组成。

### 动词类型：

- module：定义模块路径。
- go：设置期望的语言版本。
- require：依赖要求一个给定版本或者之后的特定模块。
- exclude：从使用中排除特定模块版本。
- replace：以一个不同的，模块版呢嘛提到另一个模块版本。

exclude和replace只应用在主模块的go.mod中，并且在依赖中会被忽略。



## 常见错误汇总

### go: modules disabled inside GOPATH/src by GO111MODULE=auto; see 'go help modules'

go mod init

go: modules disabled inside GOPATH/src by GO111MODULE=auto; see 'go help modules'

开启go module：

1. `set GO111MODULE=on //windows`
2. `export GO111MODULE=on //linux`

### $GOPATH/go.mod exists but should not

GO 1.11或之后模块遇到这个问题：

```
$GOPATH/go.mod exists but should not
```

  开启模块支持后（set GO111MODULE=on），并不能与$GOPATH共存，所以把$GOPATH从env中移出即可（unset GOPATH），可运行“unset GOPATH && make”

