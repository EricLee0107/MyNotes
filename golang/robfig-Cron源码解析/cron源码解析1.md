## Cron.go

`cron.go`中基本包含了Cron运行所有必须的外部接口，包含：

- 创建一个Cron的 `func New(opts ...Option) *Cron`
- 为Cron添加任务的`func (c *Cron) AddFunc(spec string, cmd func()) (EntryID, error)`和`func (c *Cron) AddJob(spec string, cmd Job) (EntryID, error`
- 用于查询Cron所有任务的`func (c *Cron) Entries() []Entry`（获取到的时任务的拷贝）
- 用于查询Cron设置的时区信息的`func (c *Cron) Location() *time.Location`
- 用于查询Cron特定ID的任务的`func (c *Cron) Entry(id EntryID) Entr`
- 删除Cron的特定ID的任务
- 启动Cron，Cron开始运行：
- 停止Cron：`func (c *Cron) Stop() context.Context`





### func New(opts ...Option) *Cron

**函数功能：**

创建一个Cron结构，并且对其进行初始化，然后返回创建好的Cron地址

**函数参数：**

opts: 主要是一些对Cron内部字段值进行二次重写的一些函数（func）,比如传入`WithLocation`函数来设置Cron的时区。

**函数返回值：**

返回一个初始化之后的Cron地址

**源代码：**

```go
func New(opts ...Option) *Cron {
	c := &Cron{
		entries:   nil,
		chain:     NewChain(),
		add:       make(chan *Entry),
		stop:      make(chan struct{}),
		snapshot:  make(chan chan []Entry),
		remove:    make(chan EntryID),
		running:   false,
		runningMu: sync.Mutex{},
		logger:    DefaultLogger,
		location:  time.Local,
		parser:    standardParser,
	}
	for _, opt := range opts {
		opt(c)
	}
	return c
}
```

整个New函数功能非常简单，先生成一个Cron，然后对Cron进行执行所有opts来设置Cron内部字段。

Cron结构源码及字段用途：

```go
type Cron struct {
	entries   []*Entry          // 存储所有的任务（调度）
	chain     Chain             // 当前cron所使用的封装接口（所有job需要在调用前先调用chain），主要用于对cron的一些执行情况做封装，比如skipIfLastRunning
	stop      chan struct{}     // 一个空结构的通道，用于接收停止整个Cron的信号
	add       chan *Entry       // 一个*Entry的通道，用于接收在cron运行过程中新加入的任务的信号
	remove    chan EntryID      // 一个EnteryID的通道，用于接收cron在运行中移除某个任务的信号
	snapshot  chan chan []Entry // 一个用于临时检查当前cron运行的所有任务条目的备份
	running   bool              // 当前cron的运行状态
	logger    Logger            // 指定log打印接口
	runningMu sync.Mutex        // 运行锁，用于修改运行中的cron数据，比如增加任务和移除任务
	location  *time.Location    // 时区信息
	parser    ScheduleParser    // 时间规范解析器（用于指定时间格式），可以定制自己的时间规则
	nextID    EntryID           // 任务ID标识，用于在生成新的任务是给任务指定ID，任务ID可以用于删除任务（remove的EntryID）或者查询任务
	jobWaiter sync.WaitGroup    // 任务计数器，保证任务启动的goroutinue在cron关闭时全部执行完成
}
```

Cron在创建时有三个字段是通过调用其他函数来初始化的：

第一个，`chain`字段，通过调用`NewChain()`函数来赋值。



第二个，`logger`字段，通过调用`DefaultLogger`来赋值。



第三个，`parser`字段，通过调用`standardParser`来赋值。

`standardParser`的具体源码如下：

```go
// parser.go
var standardParser = NewParser(
	Minute | Hour | Dom | Month | Dow | Descriptor,
)

func NewParser(options ParseOption) Parser {
	optionals := 0
	if options&DowOptional > 0 {
		optionals++
	}
	if options&SecondOptional > 0 {
		optionals++
	}
	if optionals > 1 {
		panic("multiple optionals may not be configured")
	}
	return Parser{options}
}
```

`standardParser`是一个通过`NewParser()`生成的有固定时间格式的一个时间解析器。具体的格式为：支持分钟、小时、每月的第几天、月份、每周第几天五个字段且允许使用描述符。

`NewParser`函数定义的时间格式有两个字段(SecondOptional和DowOptional)可以将对应字段设为可选字段，但每个解释器只允许有一个可选字段，所以当可选数量大于1时会引发panic。





### func (c *Cron) AddJob(spec string, cmd Job) (EntryID, error)

**函数功能：**

给Cron添加新的任务（调度）

**函数参数：**

spec: 一个复合时间格式规范的调度规则字符串，如果这个规范中没有给出时间格式的时区，则会使用Cron的时区作为默认时区。

cmd: 当满足调度规则后需要执行的原始任务（相对被封装后的WrappedJob，后面会对WrappedJob做详细说明）

**函数返回值：**

EntryID: 每个调度任务的唯一标识（在单个Cron中唯一）

error: 过程中遇到的错误返回

**源代码：**

```go
func (c *Cron) AddJob(spec string, cmd Job) (EntryID, error) {
	schedule, err := c.parser.Parse(spec)
	if err != nil {
		return 0, err
	}
	return c.Schedule(schedule, cmd), nil
}
```



这段代码主要就是两个功能： 

1. 通过Parse时间解析器解析传入的时间字符串
2. 通过Schedule创建一个调度任务（Entry）并加入到cron中



```go
func (p Parser) Parse(spec string) (Schedule, error) {
	if len(spec) == 0 {
		return nil, fmt.Errorf("empty spec string")
	}

	// Extract timezone if present
	var loc = time.Local
	if strings.HasPrefix(spec, "TZ=") || strings.HasPrefix(spec, "CRON_TZ=") {
		var err error
		i := strings.Index(spec, " ")
		eq := strings.Index(spec, "=")
		if loc, err = time.LoadLocation(spec[eq+1 : i]); err != nil {
			return nil, fmt.Errorf("provided bad location %s: %v", spec[eq+1:i], err)
		}
		spec = strings.TrimSpace(spec[i:])
	}

	// Handle named schedules (descriptors), if configured
	if strings.HasPrefix(spec, "@") {
		if p.options&Descriptor == 0 {
			return nil, fmt.Errorf("parser does not accept descriptors: %v", spec)
		}
		return parseDescriptor(spec, loc)
	}

	// Split on whitespace.
	fields := strings.Fields(spec)

	// Validate & fill in any omitted or optional fields
	var err error
	fields, err = normalizeFields(fields, p.options)
	if err != nil {
		return nil, err
	}

	field := func(field string, r bounds) uint64 {
		if err != nil {
			return 0
		}
		var bits uint64
		bits, err = getField(field, r)
		return bits
	}

	var (
		second     = field(fields[0], seconds)
		minute     = field(fields[1], minutes)
		hour       = field(fields[2], hours)
		dayofmonth = field(fields[3], dom)
		month      = field(fields[4], months)
		dayofweek  = field(fields[5], dow)
	)
	if err != nil {
		return nil, err
	}

	return &SpecSchedule{
		Second:   second,
		Minute:   minute,
		Hour:     hour,
		Dom:      dayofmonth,
		Month:    month,
		Dow:      dayofweek,
		Location: loc,
	}, nil
}
```



### func (c *Cron) AddFunc(spec string, cmd func())

**函数功能：** 对Add

**函数参数：**

**函数返回值：**

**源代码：**







### func (c *Cron) Entries() []Entry

**函数功能：**

**函数参数：**

**函数返回值：**

**源代码：**



###  func (c *Cron) Location() *time.Location

**函数功能：**

**函数参数：**

**函数返回值：**

**源代码：**



### func (c *Cron) Entry(id EntryID) Entry

**函数功能：**

**函数参数：**

**函数返回值：**

**源代码：**



### func (c *Cron) Remove(id EntryID)

**函数功能：**

**函数参数：**

**函数返回值：**

**源代码：**



### func (c *Cron) Start()

**函数功能：**

**函数参数：**

**函数返回值：**

**源代码：**



### func (c *Cron) Stop() context.Context

**函数功能：**

**函数参数：**

**函数返回值：**

**源代码：**

