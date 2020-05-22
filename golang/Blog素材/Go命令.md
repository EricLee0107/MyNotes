



# Go命令

## go bug

### 概述

向go的github上提交bug报告,报告中包括一些有用的系统信息



## go build

### 概述

用于编译我们指定的源码文件或代码包及他们的依赖包。当编译包时，忽略已“_test.go”结尾的文件。

## 用法

```go
go build [-o output] [-i] [build flags] [packages]
```

### 详解

#### 标记

- `-i`:在build过程中安装那些编译目标依赖的且未被安装的代码包。
- `-a`:强行对所有涉及道德代码包（包括标准库中的代码包）进行重新构建，即使它们已经是最新的了
- `-o`:指定输出文件的名称，当使用此标记时，不能同时对多个代码包进行编译。
- `-n`:打印编译期间所用到的其他命令，但是并不真正执行它们。
- `-v`:打印出那些被编译的代码包的名字
- `-x`:打印编译期间所用到的其他命令，并执行他们（与`-n`的差别）
- 



## go clean

### 概述

移除对象文件



### 详解

## go doc

### 概述

显示包或者符号的文档



### 详解

## go env

### 概述

打印go的运行环境信息

### 详解

## go fix

### 概述

运行go tool fix

### 详解

## go fmt

### 概述

运行gofmt进行格式化

### 详解

## go generate

### 概述

从processing source生成go

### 详解

## go get

### 概述

下载并安装包和依赖

### 详解

## go install

### 概述

编译并安装包和依赖

### 详解

## go list

### 概述

列出包

### 详解

## go mod

### 概述



### 详解

## go run 

### 概述

编译并运行go命令

### 详解

## go test

### 概述

运行测试

### 详解

## go tool

### 概述

运行go提供的工具

### 详解

## go version

### 概述

显示go版本

### 详解

## go vet

### 概述

运行go tool vet

### 详解





	Go is a tool for managing Go source code.
	
	Usage:
	
		go <command> [arguments]
	
	The commands are:
	
		bug         start a bug report
		build       compile packages and dependencies
		clean       remove object files and cached files
		doc         show documentation for package or symbol
		env         print Go environment information
		fix         update packages to use new APIs
		fmt         gofmt (reformat) package sources
		generate    generate Go files by processing source
		get         add dependencies to current module and install them
		install     compile and install packages and dependencies
		list        list packages or modules
		mod         module maintenance
		run         compile and run Go program
		test        test packages
		tool        run specified go tool
		version     print Go version
		vet         report likely mistakes in packages
	
	Use "go help <command>" for more information about a command.
	
	Additional help topics:
	
		buildmode   build modes
		c           calling between Go and C
		cache       build and test caching
		environment environment variables
		filetype    file types
		go.mod      the go.mod file
		gopath      GOPATH environment variable
		gopath-get  legacy GOPATH go get
		goproxy     module proxy protocol
		importpath  import path syntax
		modules     modules, module versions, and more
		module-get  module-aware go get
		module-auth module authentication using go.sum
		module-private module configuration for non-public modules
		packages    package lists and patterns
		testflag    testing flags
		testfunc    testing functions
	
	Use "go help <topic>" for more information about that topic.


usage: go bug

Bug opens the default browser and starts a new bug report.
The report includes useful system information.







usage: go build [-o output] [-i] [build flags] [packages]

Build compiles the packages named by the import paths,
along with their dependencies, but it does not install the results.

If the arguments to build are a list of .go files from a single directory,
build treats them as a list of source files specifying a single package.

When compiling packages, build ignores files that end in '_test.go'.

When compiling a single main package, build writes
the resulting executable to an output file named after
the first source file ('go build ed.go rx.go' writes 'ed' or 'ed.exe')
or the source code directory ('go build unix/sam' writes 'sam' or 'sam.exe').
The '.exe' suffix is added when writing a Windows executable.

When compiling multiple packages or a single non-main package,
build compiles the packages but discards the resulting object,
serving only as a check that the packages can be built.

The -o flag forces build to write the resulting executable or object
to the named output file or directory, instead of the default behavior described
in the last two paragraphs. If the named output is a directory that exists,
then any resulting executables will be written to that directory.

The -i flag installs the packages that are dependencies of the target.

The build flags are shared by the build, clean, get, install, list, run,
and test commands:

	-a
		force rebuilding of packages that are already up-to-date.
	-n
		print the commands but do not run them.
	-p n
		the number of programs, such as build commands or
		test binaries, that can be run in parallel.
		The default is the number of CPUs available.
	-race
		enable data race detection.
		Supported only on linux/amd64, freebsd/amd64, darwin/amd64, windows/amd64,
		linux/ppc64le and linux/arm64 (only for 48-bit VMA).
	-msan
		enable interoperation with memory sanitizer.
		Supported only on linux/amd64, linux/arm64
		and only with Clang/LLVM as the host C compiler.
		On linux/arm64, pie build mode will be used.
	-v
		print the names of packages as they are compiled.
	-work
		print the name of the temporary work directory and
		do not delete it when exiting.
	-x
		print the commands.
	
	-asmflags '[pattern=]arg list'
		arguments to pass on each go tool asm invocation.
	-buildmode mode
		build mode to use. See 'go help buildmode' for more.
	-compiler name
		name of compiler to use, as in runtime.Compiler (gccgo or gc).
	-gccgoflags '[pattern=]arg list'
		arguments to pass on each gccgo compiler/linker invocation.
	-gcflags '[pattern=]arg list'
		arguments to pass on each go tool compile invocation.
	-installsuffix suffix
		a suffix to use in the name of the package installation directory,
		in order to keep output separate from default builds.
		If using the -race flag, the install suffix is automatically set to race
		or, if set explicitly, has _race appended to it. Likewise for the -msan
		flag. Using a -buildmode option that requires non-default compile flags
		has a similar effect.
	-ldflags '[pattern=]arg list'
		arguments to pass on each go tool link invocation.
	-linkshared
		build code that will be linked against shared libraries previously
		created with -buildmode=shared.
	-mod mode
		module download mode to use: readonly, vendor, or mod.
		See 'go help modules' for more.
	-modcacherw
		leave newly-created directories in the module cache read-write
		instead of making them read-only.
	-modfile file
		in module aware mode, read (and possibly write) an alternate go.mod
		file instead of the one in the module root directory. A file named
		"go.mod" must still be present in order to determine the module root
		directory, but it is not accessed. When -modfile is specified, an
		alternate go.sum file is also used: its path is derived from the
		-modfile flag by trimming the ".mod" extension and appending ".sum".
	-pkgdir dir
		install and load all packages from dir instead of the usual locations.
		For example, when building with a non-standard configuration,
		use -pkgdir to keep generated packages in a separate location.
	-tags tag,list
		a comma-separated list of build tags to consider satisfied during the
		build. For more information about build tags, see the description of
		build constraints in the documentation for the go/build package.
		(Earlier versions of Go used a space-separated list, and that form
		is deprecated but still recognized.)
	-trimpath
		remove all file system paths from the resulting executable.
		Instead of absolute file system paths, the recorded file names
		will begin with either "go" (for the standard library),
		or a module path@version (when using modules),
		or a plain import path (when using GOPATH).
	-toolexec 'cmd args'
		a program to use to invoke toolchain programs like vet and asm.
		For example, instead of running asm, the go command will run
		'cmd args /path/to/asm <arguments for asm>'.

The -asmflags, -gccgoflags, -gcflags, and -ldflags flags accept a
space-separated list of arguments to pass to an underlying tool
during the build. To embed spaces in an element in the list, surround
it with either single or double quotes. The argument list may be
preceded by a package pattern and an equal sign, which restricts
the use of that argument list to the building of packages matching
that pattern (see 'go help packages' for a description of package
patterns). Without a pattern, the argument list applies only to the
packages named on the command line. The flags may be repeated
with different patterns in order to specify different arguments for
different sets of packages. If a package matches patterns given in
multiple flags, the latest match on the command line wins.
For example, 'go build -gcflags=-S fmt' prints the disassembly
only for package fmt, while 'go build -gcflags=all=-S fmt'
prints the disassembly for fmt and all its dependencies.

For more about specifying packages, see 'go help packages'.
For more about where packages and binaries are installed,
run 'go help gopath'.
For more about calling between Go and C/C++, run 'go help c'.

Note: Build adheres to certain conventions such as those described
by 'go help gopath'. Not all projects can follow these conventions,
however. Installations that have their own conventions or that use
a separate software build system may choose to use lower-level
invocations such as 'go tool compile' and 'go tool link' to avoid
some of the overheads and design decisions of the build tool.

See also: go install, go get, go clean.