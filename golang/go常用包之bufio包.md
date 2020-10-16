#### go常用包之bufio包

> bufio包实现了待缓存的I/O操作，它封装了一个io.Reader或者io.Writer对象。创建另外一个对象（Reader 或者 Writer)，这个对象也实现了一个接口，并且提供缓存和一些文章读写的帮助。

**三个常量：**

```go
const(
    defaultBuffSize = 4096 //默认大小
)

const minReadBufferSize = 16  // 读取器的缓存最小值
const maxConsecutiveEmpty = 100 // 
```

**Reader类型**

```go
type Reader struct {
    buf          []byte
    rd           io.Reader // 客户端提供的reader
    r, w         int       // buf 读取和写入的位置
    err          error
    lastByte     int
    lastRuneSize int
}
```

Reader实现了对一个io.Reader对象的缓冲读

**Reader类型的方法**

`func (b *Reader) Reset(r io.Reader)`
Reset 丢弃缓冲区中的数据，清除任何状态，并且重新从r读取数据到缓冲区中。

`func (b *Reader) Peek(n int) ([]byte, error)`
返回没有读取的下n个字节。如果n大于b的缓冲区大小，则返回ErrBufferFull

`func (b *Reader) Discard(n int) ([]byte, error)`
抛弃n个字节，返回被抛弃的字节数。如果抛弃的字节数超过buf中的字节数，也会返回err；

`func (b *Reader) Read(p []byte) (n int, err error)`
读取数据到p，返回读取到p的字节数。
底层读取最多只会调用一次Read,因此n会小于len(p)。
在EOF之后，调用这个函数返回的会是0和io.EOF

`func (b *Reader) ReadByte() (c byte, err error)`
读取并返回一个单字节，如果没有字节可以读取，则返回error

`func (b *Reader) UnreadByte() error`
将最后的字节标志为未读，只有最后的字节可以被标志为未读。

`func (b *Reader) ReadRune() (r rune, size int, err error)`
读取单个的UTF-8编码的Unicode字节，返回rune（符号）和它的字节大小。如果rune是课件的，它小号一个字节并返回一字节的unicode.ReplacementChar（U+FFFD)。

`func (b *Reader) UnreadRune() error`
将最后一个rune设置为未读

`func (b *Reader) Buffered() int`
返回当前缓存的刻度字节数

`func (b *Reader) ReadSlice(delim byte) (line []byte, err error)`
读取输入直到第一次终止符发生的时候，返回一个纸箱缓冲中字节的slice。

`func (b *Reader) ReadLine() (line []byte, isPrefix bool, err error)`
f返回一个非空行或者返回error

`func (b *Reader) ReadBytes(delim byte)`
读取输入直到第一次终止符发生的时候，返回的slice包含从当前到终止符的内容（包括终止符）。

`func (b *Reader) ReadString(delim byte) (line string, err error)`
读取输入直到第一次终止符发生的时候，返回的string包含从当前到终止符的内容（包括终止符）。

`func (b *Reader) WriteTo(w io.Writer) (n int64, err error)`
WriteTo 实现了io.WriteTo，将数据写入w中。

`func (b *Reader) writeBuf(w io.Writer) (int64, error)`
将Reder的缓存写到writer中

`func (r *Reader) Size() int`
返回Reader的大小。

`func (b *Reader) fill()`
file 读取一个新的块到缓存中

**Reader相关函数**

```
func NewReaderSize(rd io.Reader, size int) *Reader`
返回一个新的读取器，这个读取器的缓存一定大于指定的size。如果io.Reader参数已经有一个足够大缓存的读取器，则直接返回这个Reader。
`func NewReader(rd io.Reader) *Reader`
返回一个有默认尺寸缓存的新的读取器，等价于`NewReaderSize(rd, defaultBuffSize)
```

**Writer相关函数**
`func NewWriterSize(w io.Writer, size int) *Writer`
返回一个新的Writer,他的缓存一定大于指定的size。如果io.Writer参数已经有一个足够大的缓存的Writer,则直接返回底层的Writer。
`func NewWriter(w io.Writer) *Writer`
返回一个有默认尺寸缓存的新的Writer,等价于`NewWriter(w io.Writer, defaultBuffSize)`

**ReadWriter相关函数**
`func NewReadWriter(r *Reader, w *Writer) *ReadWriter`
返回一个新的ReadWriter来进行r和w的调度。

`func NewReader`
NewReader 返回的是什么类型？为什么有ReadLine方法？ ReadLine方法获取的Line是字符数组？string函数是什么函数？line的类型及包含的方法？

![55d645de-80de-4e10-b257-8855d3baa58f](https://eric-typora-img.oss-cn-beijing.aliyuncs.com/typora/202009/04/183015-384527.png)

