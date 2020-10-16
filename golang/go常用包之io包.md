### go常用包之io包



#### io包中的函数

`func WriteString(w Writer, s String)(n int, err error)`
**功能：**将字符串s写入到w中,如果w实现了WriteString方法，则直接调用它。
**返回值：**返回写入的字节数和写入过程中遇到的任何错误。
**实例：**

```
package main

import "io"
import "os"

func main() {
    // os.Stdout 实现了 Writer 接口
    io.WriteString(os.Stdout, "Hello World!")
    // Hello World!
}
```

**源码：**

```
func WriteString(w Writer, s string) (n int, err error) {
    if sw, ok := w.(stringWriter); ok {
        return sw.WriteString(s)
    }
    return w.Write([]byte(s))
}
func ReadAtLeast(r Reader, buf []Byte, min int)(n int, err error)
```

**返回值：** 返回读取的字节数和读取过程中遇到的任何错误

**功能：**从r中读取数据到buf中，要求至少读取min个字节

**错误返回类型：**

- err返回ErrUnexpectedEOF:表示n<min
- err返回EOF：表示r中无可读取数据
- err返回ErrShortBuffer:表示min大于len(buf)
- err返回nil:表示n>=min

**实例：**

```
package main

import "io"
import "strings"
import "fmt"

func main() {
    r := strings.NewReader("Hello World!")
    b := make([]byte, 32)
    n, err := io.ReadAtLeast(r, b, 20)
    fmt.Printf("%s\n%d, %v", b, n, err)
    // Hello World!
    // 12, unexpected EOF
}
```

**源码：**

```
func ReadAtLeast(r Reader, buf []byte, min int) (n int, err error) {
    if len(buf) < min {
        return 0, ErrShortBuffer
    }
    for n < min && err == nil {
        var nn int
        nn, err = r.Read(buf[n:])
        n += nn
    }
    if n >= min {
        err = nil
    } else if n > 0 && err == EOF {
        err = ErrUnexpectedEOF
    }
    return
}
```

`func ReadFull(r Reader, buf []byte)(n int, err error)`
**功能：**和ReadAtLeast一样，默认min=len(buf)
**返回值：**和ReadAtLest一样

`func Copy(dst Writer, src Reader)(written int64, err error)`
**功能：**从src中赋值数据到dst中，直到所有数据复制完毕
**返回值：**返回复制的字节数和复制时遇到的第一个错误。Copy成功返回 err == nil,
而不是err == EOF
**特殊性：**

1. Copy 被定义为从src读取直到EOF为止，收益以Copy不会将来自Read的EOF当做错误来报告。
2. 如果src实现了WriteTo接口，`Copy(dst Writer, src Reader)` 等于`src.WritenTo(dst)`
3. 如果dst实现了ReadFrom接口， `Copy(dst Writer, src Reader)` 等于`dst.ReadFrom(src)`

**实例：**

```go
package main

import "os"
import "strings"
//import "fmt"
import "io"
func main() {
    r := strings.NewReader("Hello World!")
    n, err := io.Copy(os.Stdout, r)
    fmt.Printf("\n%d, %v", n, err)
    // Hello World!
    // 12, <nil>
}
```

**源码：**

```go
func Copy(dst Writer, src Reader) (written int64, err error) {
    return copyBuffer(dst, src, nil)
}

// CopyBuffer is identical to Copy except that it stages through the
// provided buffer (if one is required) rather than allocating a
// temporary one. If buf is nil, one is allocated; otherwise if it has
// zero length, CopyBuffer panics.
func CopyBuffer(dst Writer, src Reader, buf []byte) (written int64, err error) {
    if buf != nil && len(buf) == 0 {
        panic("empty buffer in io.CopyBuffer")
    }
    return copyBuffer(dst, src, buf)
}

// copyBuffer is the actual implementation of Copy and CopyBuffer.
// if buf is nil, one is allocated.
func copyBuffer(dst Writer, src Reader, buf []byte) (written int64, err error) {
    // If the reader has a WriteTo method, use it to do the copy.
    // Avoids an allocation and a copy.
    if wt, ok := src.(WriterTo); ok {
        return wt.WriteTo(dst)
    }
    // Similarly, if the writer has a ReadFrom method, use it to do the copy.
    if rt, ok := dst.(ReaderFrom); ok {
        return rt.ReadFrom(src)
    }
    size := 32 * 1024
    if l, ok := src.(*LimitedReader); ok && int64(size) > l.N {
        if l.N < 1 {
            size = 1
        } else {
            size = int(l.N)
        }
    }
    if buf == nil {
        buf = make([]byte, size)
    }
    for {
        nr, er := src.Read(buf)
        if nr > 0 {
            nw, ew := dst.Write(buf[0:nr])
            if nw > 0 {
                written += int64(nw)
            }
            if ew != nil {
                err = ew
                break
            }
            if nr != nw {
                err = ErrShortWrite
                break
            }
        }
        if er != nil {
            if er != EOF {
                err = er
            }
            break
        }
    }
    return written, err
}
```

`func CopyN(dst Writer, src Reader, n int64)(written int64, err error)`
**功能：**从src中复制n个字节的数据到dst中。
CopyN和Copy一样只是通过LimitReader(src,n)来限制复制字节数。
**其他：**CopyN不能直接通过src.WritenTo(dst)实现，因为限制了copy过程中存在限制读取字节数。
**源码：**

```
func CopyN(dst Writer, src Reader, n int64) (written int64, err error) {
    written, err = Copy(dst, LimitReader(src, n))
    if written == n {
        return n, nil
    }
    if written < n && err == nil {
        // src stopped early; must have been EOF.
        err = EOF
    }
    return
}
```

`func LimitReader(r Reader, n int64)Reader`
**功能：**返回一个Reader,它从r读取n个字节后以EOF结束
**其他：** 基本实现为 *LimitedReader
**源码：**

```
    // LimitedReader 从 R 读取但将返回的数据量限制为 N 字节。每调用一次 Read
    // 都将更新 N 来反射新的剩余数量。
    type LimitedReader struct {
        R Reader // underlying reader   // 基本读取器
        N int64  // max bytes remaining // 最大剩余字节
    }

    func (l *LimitedReader) Read(p []byte) (n int, err error) {
        if l.N <= 0 {
            return 0, EOF
        }
        if int64(len(p)) > l.N {
            p = p[0:l.N]
        }
        n, err = l.R.Read(p)
        l.N -= int64(n)
        return
    }
```

`func NewSectionReader(r ReaderAt, off int64, n int64)*SectionReader`
**功能：**从r中off的位置读取n个字节的数据，读取完毕后返回EOF
**返回值：**返回一个SectionReader

`func(s *SectionReader)Size()`
**功能：**返回s中被限制读取的字节数

`func TeeReader(r Reader, w Writer)Reader`
**功能：** 返回一个将其从r读取的数据写入w的Reader接口，使r在读取数据的同时，自动向w中写入数据。
**返回值：**返回一个Reader接口。
**特性：**

1. 没有内部的缓冲区，（即，写入必须在读取完成前完成）。
2. 写入时发生的任何错误都作为读取错误返回。

**实例：**

```go
package main

import "os"
import "strings"
import "io"
func main() {
    r := strings.NewReader("Hello World!")
    tr := io.TeeReader(r, os.Stdout)
    b := make([]byte, 32)
    tr.Read(b)
    // Hello World!
}
```

**源码：**

```
func TeeReader(r Reader, w Writer) Reader {
    return &teeReader{r, w}
}

type teeReader struct {
    r Reader
    w Writer
}

func (t *teeReader) Read(p []byte) (n int, err error) {
    n, err = t.r.Read(p)
    if n > 0 {
        if n, err := t.w.Write(p[:n]); err != nil {
            return n, err
        }
    }
    return
}
```

更多功能参考：
[golang io包源码地址](https://github.com/golang/go/blob/master/src/io/io.go)

