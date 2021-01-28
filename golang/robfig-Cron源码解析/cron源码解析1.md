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

源代码如下：

```go
func NewChain(c ...JobWrapper) Chain {
	return Chain{c}
}
```

`NewChain`主要是生成一个`Chain`结构的变量，并将传入的封装器存入`Chain`变量中，在调用Then时会通过传入的封装器进行封装，具体过程如下：

```go
func (c Chain) Then(j Job) Job {
	for i := range c.wrappers {
		j = c.wrappers[len(c.wrappers)-i-1](j) // 从封装器最后一个开始封装
	}
	return j
}
```

`NewChain(m1,m2,m3).Then(job)` 封装完之后为`m1(m2(m3(job)))`



第二个，`logger`字段，通过调用`DefaultLogger`来赋值。

```go
var DefaultLogger Logger = PrintfLogger(log.New(os.Stdout, "cron: ", log.LstdFlags))

type Logger interface {
	// Info logs routine messages about cron's operation.
	Info(msg string, keysAndValues ...interface{})
	// Error logs an error condition.
	Error(err error, msg string, keysAndValues ...interface{})
}


func PrintfLogger(l interface{ Printf(string, ...interface{}) }) Logger {
	return printfLogger{l, false}
}

type printfLogger struct {
	logger  interface{ Printf(string, ...interface{}) }
	logInfo bool
}

func (pl printfLogger) Info(msg string, keysAndValues ...interface{}) {
	if pl.logInfo {
		keysAndValues = formatTimes(keysAndValues)
		pl.logger.Printf(
			formatString(len(keysAndValues)),
			append([]interface{}{msg}, keysAndValues...)...)
	}
}

func (pl printfLogger) Error(err error, msg string, keysAndValues ...interface{}) {
	keysAndValues = formatTimes(keysAndValues)
	pl.logger.Printf(
		formatString(len(keysAndValues)+2),
		append([]interface{}{msg, "error", err}, keysAndValues...)...)
}

```

主要是返回一个`Logger`类型的接口，具体实现时`printfLogger`。



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

可以通过`WithParser`函数来设置自定义的时间解析器。





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



时间解析实现如下：

```go
func (p Parser) Parse(spec string) (Schedule, error) {
	if len(spec) == 0 {
		return nil, fmt.Errorf("empty spec string")
	}

	// 检查时间字符串中是否包含时区，如果包含则解析到loc变量中存储，
	// 解析的规则是："TZ="和"CRON_TZ="来标识，并且放在字符串的开始阶段，通过空格与后面时间进行分割。
	// 比如："CRON_TZ=UTC 0 5 * * * *"
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

    // 处理描述符，当时间解释器中配置了允许使用描述符时，则可以通过@标识描述符
	if strings.HasPrefix(spec, "@") {
		if p.options&Descriptor == 0 {
			return nil, fmt.Errorf("parser does not accept descriptors: %v", spec)
		}
        // 具体的描述符解析（后面单独说明：详见内部接口）
		return parseDescriptor(spec, loc)
	}

	// 时间格式字符串各个字段值之间是通过空格分割的，这里的spec是去除时区信息之后剩余的时间信息。
	fields := strings.Fields(spec)

	// Validate & fill in any omitted or optional fields
    
	var err error
    // 根据给定的时间格式规范解析给定的字符串，最终解析成程序规范的格式，未定义的字段会以默认值代替（详见内部接口）
	fields, err = normalizeFields(fields, p.options)
	if err != nil {
		return nil, err
	}

	field := func(field string, r bounds) uint64 {
		if err != nil {
			return 0
		}
		var bits uint64
        // 根据字段值，生成字段位标识（详见内部接口）
		bits, err = getField(field, r)
		return bits
	}
	// 初始化每个字段的标识位，标识各个字段所匹配的值
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



```go
func (c *Cron) Schedule(schedule Schedule, cmd Job) EntryID {
	c.runningMu.Lock()
	defer c.runningMu.Unlock()
	c.nextID++
	entry := &Entry{
		ID:         c.nextID,
		Schedule:   schedule,
		WrappedJob: c.chain.Then(cmd),
		Job:        cmd,
	}
	if !c.running {
		c.entries = append(c.entries, entry)
	} else {
		c.add <- entry
	}
	return entry.ID
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















## 内部接口

### func parseDescriptor(descriptor string, loc *time.Location) (Schedule, error)



```go
// 解析所有的描述符，比如@monthly 代表每月的第一秒， 等价于 1 1 1 1 * *  
func parseDescriptor(descriptor string, loc *time.Location) (Schedule, error) {
	switch descriptor {
	case "@yearly", "@annually":
		return &SpecSchedule{
			Second:   1 << seconds.min,
			Minute:   1 << minutes.min,
			Hour:     1 << hours.min,
			Dom:      1 << dom.min,
			Month:    1 << months.min,
			Dow:      all(dow),
			Location: loc,
		}, nil

	case "@monthly":
		return &SpecSchedule{
			Second:   1 << seconds.min,
			Minute:   1 << minutes.min,
			Hour:     1 << hours.min,
			Dom:      1 << dom.min,
			Month:    all(months),
			Dow:      all(dow),
			Location: loc,
		}, nil

	case "@weekly":
		return &SpecSchedule{
			Second:   1 << seconds.min,
			Minute:   1 << minutes.min,
			Hour:     1 << hours.min,
			Dom:      all(dom),
			Month:    all(months),
			Dow:      1 << dow.min,
			Location: loc,
		}, nil

	case "@daily", "@midnight":
		return &SpecSchedule{
			Second:   1 << seconds.min,
			Minute:   1 << minutes.min,
			Hour:     1 << hours.min,
			Dom:      all(dom),
			Month:    all(months),
			Dow:      all(dow),
			Location: loc,
		}, nil

	case "@hourly":
		return &SpecSchedule{
			Second:   1 << seconds.min,
			Minute:   1 << minutes.min,
			Hour:     all(hours),
			Dom:      all(dom),
			Month:    all(months),
			Dow:      all(dow),
			Location: loc,
		}, nil

	}

	const every = "@every "
	if strings.HasPrefix(descriptor, every) {
		duration, err := time.ParseDuration(descriptor[len(every):])
		if err != nil {
			return nil, fmt.Errorf("failed to parse duration %s: %s", descriptor, err)
		}
		return Every(duration), nil
	}

	return nil, fmt.Errorf("unrecognized descriptor: %s", descriptor)
}
```



### func normalizeFields(fields []string, options ParseOption) ([]string, error)

```go

func normalizeFields(fields []string, options ParseOption) ([]string, error) {
    // 检查是否有可选字段，如果有则给时间范式中增加对应字段
	optionals := 0
	if options&SecondOptional > 0 {
		options |= Second
		optionals++
	}
	if options&DowOptional > 0 {
		options |= Dow
		optionals++
	}
    // 一个时间范式中只允许有一个可选子弹
	if optionals > 1 {
		return nil, fmt.Errorf("multiple optionals may not be configured")
	}

    // 获取范式允许的字段个数范围
	max := 0
    // 最大值为optionas中标识的固定字段（包括可选字段，但不包含描述符字段）
	for _, place := range places {
		if options&place > 0 {
			max++
		}
	}
    // 最小值为最大值减去可选字段个数
	min := max - optionals

	// 检查给定时间字符串是否满足字段数要求
	if count := len(fields); count < min || count > max {
		if min == max {
			return nil, fmt.Errorf("expected exactly %d fields, found %d: %s", min, count, fields)
		}
		return nil, fmt.Errorf("expected %d to %d fields, found %d: %s", min, max, count, fields)
	}

	// 对于有可选字段但未传入的则设置为默认值
	if min < max && len(fields) == min {
		switch {
		case options&DowOptional > 0:
			fields = append(fields, defaults[5]) // TODO: improve access to default
		case options&SecondOptional > 0:
			fields = append([]string{defaults[0]}, fields...)
		default:
			return nil, fmt.Errorf("unknown optional field")
		}
	}

	// 将给的字符串补充成通用格式（不需要的字段补充默认值）
	n := 0
	expandedFields := make([]string, len(places))
	copy(expandedFields, defaults)
	for i, place := range places {
		if options&place > 0 {
			expandedFields[i] = fields[n]
			n++
		}
	}
	return expandedFields, nil
}
```





### func getField(field string, r bounds) (uint64, error)

```go
func getField(field string, r bounds) (uint64, error) {
	var bits uint64
	// 同一个字段中的多个值之间是通过逗号分割的，所以根据逗号分割之后可以获得字段的多个值（可能是范围可能是单个值）
	ranges := strings.FieldsFunc(field, func(r rune) bool { return r == ',' })
	// 遍历字段的所有值，生成对应的位运算值。
	for _, expr := range ranges {
		bit, err := getRange(expr, r)
		if err != nil {
			return bits, err
		}
		bits |= bit
	}
	return bits, nil
}
```



### func getRange(expr string, r bounds) (uint64, error)

```go
// 返回expr代表的位（expr可以是一个值或者用'-'来表示的一个范围，或者是一个用'/'表示的每隔多久）
func getRange(expr string, r bounds) (uint64, error) {
	var (
		// 用于存储起始和结束点，以及每次跨越间隔step
		start, end, step uint
		// 分割字符，比如秒字段： 1-30/3  代表1-30秒中每3s一次，rangeAndStep[0] = "1-30" rangeAndStep[1] = "3"
		rangeAndStep     = strings.Split(expr, "/")
		// 分割范围获取范围最大最小值，lowAndHigh[0] = "1" lowAndHigh[1] = "30"
		lowAndHigh       = strings.Split(rangeAndStep[0], "-")
		singleDigit      = len(lowAndHigh) == 1
		err              error
	)

	// 如果字段是*或者? 则代表允许范围内任何值
	var extra uint64
	if lowAndHigh[0] == "*" || lowAndHigh[0] == "?" {
		start = r.min
		end = r.max
		extra = starBit
	} else {
		start, err = parseIntOrName(lowAndHigh[0], r.names)
		if err != nil {
			return 0, err
		}
		// 如果只有一个值，则代表起始和结束值相同
		switch len(lowAndHigh) {
		case 1:
			end = start
		case 2:
			// 两个值代表起始不同，单独解析结束值
			end, err = parseIntOrName(lowAndHigh[1], r.names)
			if err != nil {
				return 0, err
			}
		default:
			// 其他长度则属于错误
			return 0, fmt.Errorf("too many hyphens: %s", expr)
		}
	}

	// 解析每个间隔步长，如果1个参数，则代表未设置步长，默认为1。
	switch len(rangeAndStep) {
	case 1:
		step = 1
	case 2:
		step, err = mustParseInt(rangeAndStep[1])
		if err != nil {
			return 0, err
		}

		// 如果存在'/'则代表前面需要是一个范围，如果给定N/Setp,实际代表的时"N-Max/step",所以这里会对只给定了一个值的范围值补充结束值为字段最大值
		if singleDigit {
			end = r.max
		}
		// 对于步数为1的范围，后续处理extra有特殊操作,非步数为1的无意义，可以设置为0
		if step > 1 {
			extra = 0
		}
	default:
		return 0, fmt.Errorf("too many slashes: %s", expr)
	}

	if start < r.min {
		return 0, fmt.Errorf("beginning of range (%d) below minimum (%d): %s", start, r.min, expr)
	}
	if end > r.max {
		return 0, fmt.Errorf("end of range (%d) above maximum (%d): %s", end, r.max, expr)
	}
	if start > end {
		return 0, fmt.Errorf("beginning of range (%d) beyond end of range (%d): %s", start, end, expr)
	}
	if step == 0 {
		return 0, fmt.Errorf("step of range should be a positive number: %s", expr)
	}

	// 这里的extra用于当step为1时返回的可能为一个超过64位的值，此时需要extra来截断使其最多64位
	return getBits(start, end, step) | extra, nil
}
```

### func getBits(min, max, step uint) uint64

```go
// 以给定步长，在[min,max]取step的倍数，将所有满足条件的值，设置到位标识符上。
// 这里要特别注意一点就是，这个只回取step的倍数，比如对于秒字段 ： */7 代表每7秒执行一次，但实际是所有7的倍数秒执行，在一分钟的第56秒会执行一次（56为7的倍数），在下一分钟的第0秒又会执行一次，两次间隔只有四秒。并不是每七秒一次。
func getBits(min, max, step uint) uint64 {
	var bits uint64

	// 对于1秒步长用了通过移位运算实现
	if step == 1 {
		return ^(math.MaxUint64 << (max + 1)) & (math.MaxUint64 << min)
	}

	// Else, use a simple loop.
	for i := min; i <= max; i += step {
		bits |= 1 << i
	}
	return bits
}
```

