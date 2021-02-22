### Constants
### Variables
### type ProtocolError
#### func (err *ProtocolError) Error() string
#### func CanonicalHeaderKey(s string) string
#### func DetectContentType(data []byte) string
#### func ParseHTTPVersion(vers string) (major, minor int, ok bool)
#### func ParseTime(text string) (t time.Time, err error)
#### func StatusText(code int) string
### type ConnState
#### func (c ConnState) String() string
### type Header
#### func (h Header) Get(key string) string
Get返回键对应的第一个值，如果键不存在会返回""。如要获取该键对应的值切片，请直接用规范格式的键访问map。
#### func (h Header) Set(key, value string)
Set添加键值对到h，如键已存在则会用只有新值一个元素的切片取代旧值切片。
#### func (h Header) Add(key, value string)
Add添加键值对到h，如键已存在则会将新的值附加到旧值切片后面。
#### func (h Header) Del(key string)
Del删除键值对。
#### func (h Header) Write(w io.Writer) error
Write以有线(wire)格式将头域写入w。
#### func (h Header) WriteSubset(w io.Writer, exclude map[string]bool) error
WriteSubset以有线(wire)格式将头域写入w。当exclude不为nil时，如果h的键值对的键在exclude中存在且其对应值为真，该键值对就不会被写入w。
### type Cookie
Cookie代表一个出现在HTTP回复的头域中Set-Cookie头的值里或者HTTP请求的头域中Cookie头的值里的HTTP cookie。
#### func (c *Cookie) String() string
String返回该cookie的序列化结果。如果只设置了Name和Value字段，序列化结果可用于HTTP请求的Cookie头或者HTTP回复的Set-Cookie头；如果设置了其他字段，序列化结果只能用于HTTP回复的Set-Cookie头。
### type CookieJar
### type Request
请求表示由服务器接收或由客户端发送的HTTP请求。

客户端和服务器使用情况的字段语义略有不同。除了下面字段的注释之外，请参阅Request.Write和RoundTripper的文档。
#### func NewRequest(method, urlStr string, body io.Reader) (*Request, error)
NewRequest返回给定方法，URL和可选主体的新请求。

如果提供的主体也是io.Closer，则返回的Request.Body将设置为body，并且将由客户端方法Do，Post和PostForm以及Transport.RoundTrip关闭。

NewRequest返回适用于Client.Do或Transport.RoundTrip的请求。要创建与测试服务器处理程序一起使用的请求，请使用net/http/httptest软件包中的NewRequest函数，使用ReadRequest或手动更新请求字段。有关入站和出站请求字段之间的差异，请参阅请求类型的文档。

如果body是* bytes.Buffer，* bytes.Reader或* strings.Reader类型，则返回的请求的ContentLength被设置为它的精确值（而不是-1），GetBody被填充（所以307和308重定向可以重放body），如果ContentLength为0，则Body设置为NoBody。
#### func ReadRequest(b *bufio.Reader) (req *Request, err error)
ReadRequest从b读取并解析出一个HTTP请求。（本函数主要用在服务端从下层获取请求）
#### func (r *Request) ProtoAtLeast(major, minor int) bool
ProtoAtLeast报告该请求使用的HTTP协议版本至少是major.minor。
#### func (r *Request) UserAgent() string
UserAgent返回请求中的客户端用户代理信息（请求的User-Agent头）。
#### func (r *Request) Referer() string
Referer返回请求中的访问来路信息。（请求的Referer头）

Referer在请求中就是拼错了的，这是HTTP早期就有的错误。该值也可以从用Header["Referer"]获取； 让获取Referer字段变成方法的好处是，编译器可以诊断使用正确单词拼法的req.Referrer()的程序，但却不能诊断使用Header["Referrer"]的程序。
#### func (r *Request) AddCookie(c *Cookie)
AddCookie向请求添加一个cookie。根据RFC 6265第5.4节的规定，AddCookie不会附加多个Cookie标题字段。这意味着所有的cookies（如果有的话）被写入同一行，并以分号分隔。
#### func (r *Request) SetBasicAuth(username, password string)
SetBasicAuth使用提供的用户名和密码，采用HTTP基本认证，设置请求的Authorization头。HTTP基本认证会明码传送用户名和密码。
#### func (r *Request) Write(w io.Writer) error
#### func (r *Request) WriteProxy(w io.Writer) error
#### func (r *Request) Cookies() []*Cookie
Cookies解析并返回随请求发送的HTTP Cookie。
#### func (r *Request) Cookie(name string) (*Cookie, error)
Cookie返回请求中名为name的cookie，如果未找到该cookie返回`nil,ErrNoCookie`。如果多个cookie匹配给定名称，则只会返回一个cookie。
#### func (r *Request) ParseForm() error
ParseForm解析URL中的查询字符串，并将解析结果更新到r.Form字段。

对于POST或PUT请求，ParseForm还会将body当作表单解析，并将结果既更新到r.PostForm也更新到r.Form。解析结果中，POST或PUT请求主体要优先于URL查询字符串（同名变量，主体的值在查询字符串的值前面）。

如果请求的主体的大小没有被MaxBytesReader函数设定限制，其大小默认限制为开头10MB。

ParseMultipartForm会自动调用ParseForm。重复调用本方法是无意义的。
#### func (r *Request) ParseMultipartForm(maxMemory int64) error
ParseMultipartForm将请求的主体作为multipart/form-data解析。请求的整个主体都会被解析，得到的文件记录最多maxMemery字节保存在内存，其余部分保存在硬盘的temp文件里。如果必要，ParseMultipartForm会自行调用ParseForm。重复调用本方法是无意义的。
#### func (r *Request) FormValue(key string) string
FormValue返回key为键查询r.Form字段得到结果[]string切片的第一个值。POST和PUT主体中的同名参数优先于URL查询字符串。如果必要，本函数会隐式调用ParseMultipartForm和ParseForm。
#### func (r *Request) PostFormValue(key string) string
PostFormValue返回key为键查询r.PostForm字段得到结果[]string切片的第一个值。如果必要，本函数会隐式调用ParseMultipartForm和ParseForm。
#### func (r *Request) FormFile(key string) (multipart.File, *multipart.FileHeader, error)
FormFile返回以key为键查询r.MultipartForm字段得到结果中的第一个文件和它的信息。如果必要，本函数会隐式调用ParseMultipartForm和ParseForm。查询失败会返回ErrMissingFile错误。
#### func (r *Request) MultipartReader() (*multipart.Reader, error)
如果请求是multipart/form-data POST请求，MultipartReader返回一个multipart.Reader接口，否则返回nil和一个错误。使用本函数代替ParseMultipartForm，可以将r.Body作为流处理。
### type Response
```go
type Response struct {
    Status     string // 例如"200 OK"
    StatusCode int    // 例如200
    Proto      string // 例如"HTTP/1.0"
    ProtoMajor int    // 例如1
    ProtoMinor int    // 例如0
    // Header保管头域的键值对。
    // 如果回复中有多个头的键相同，Header中保存为该键对应用逗号分隔串联起来的这些头的值
    // （参见RFC 2616 Section 4.2）
    // 被本结构体中的其他字段复制保管的头（如ContentLength）会从Header中删掉。
    //
    // Header中的键都是规范化的，参见CanonicalHeaderKey函数
    Header Header
    // Body代表回复的主体。
    // Client类型和Transport类型会保证Body字段总是非nil的，即使回复没有主体或主体长度为0。
    // 关闭主体是调用者的责任。
    // 如果服务端采用"chunked"传输编码发送的回复，Body字段会自动进行解码。
    Body io.ReadCloser
    // ContentLength记录相关内容的长度。
    // 其值为-1表示长度未知（采用chunked传输编码）
    // 除非对应的Request.Method是"HEAD"，其值>=0表示可以从Body读取的字节数
    ContentLength int64
    // TransferEncoding按从最外到最里的顺序列出传输编码，空切片表示"identity"编码。
    TransferEncoding []string
    // Close记录头域是否指定应在读取完主体后关闭连接。（即Connection头）
    // 该值是给客户端的建议，Response.Write方法的ReadResponse函数都不会关闭连接。
    Close bool
    // Trailer字段保存和头域相同格式的trailer键值对，和Header字段相同类型
    Trailer Header
    // Request是用来获取此回复的请求
    // Request的Body字段是nil（因为已经被用掉了）
    // 这个字段是被Client类型发出请求并获得回复后填充的
    Request *Request
    // TLS包含接收到该回复的TLS连接的信息。 对未加密的回复，本字段为nil。
    // 返回的指针是被（同一TLS连接接收到的）回复共享的，不应被修改。
    TLS *tls.ConnectionState
}
```
Response代表一个HTTP请求的回复。
#### func ReadResponse(r *bufio.Reader, req *Request) (*Response, error)
ReadResponse从r读取并返回一个HTTP 回复。req参数是可选的，指定该回复对应的请求（即是对该请求的回复）。如果是nil，将假设请求是GET请求。客户端必须在结束resp.Body的读取后关闭它。读取完毕并关闭后，客户端可以检查resp.Trailer字段获取回复的trailer的键值对。（本函数主要用在客户端从下层获取回复）
#### func (r *Response) ProtoAtLeast(major, minor int) bool
ProtoAtLeast报告该回复使用的HTTP协议版本至少是major.minor。
#### func (r *Response) Cookies() []*Cookie
Cookies解析并返回该回复中的Set-Cookie头设置的cookie。
#### func (r *Response) Location() (*url.URL, error)
Location返回该回复的Location头设置的URL。相对地址的重定向会相对于该回复对应的请求来确定绝对地址。如果回复中没有Location头，会返回nil, ErrNoLocation。
#### func (r *Response) Write(w io.Writer) error
Write以有线格式将回复写入w（用于将回复写入下层TCPConn等）。本方法会考虑如下字段：
```go
StatusCode
ProtoMajor
ProtoMinor
Request.Method
TransferEncoding
Trailer
Body
ContentLength
Header（不规范的键名和它对应的值会导致不可预知的行为）
```
Body字段在发送完回复后会被关闭。
### type ResponseWriter
```go
type ResponseWriter interface {
    // Header返回一个Header类型值，该值会被WriteHeader方法发送。
    // 在调用WriteHeader或Write方法后再改变该对象是没有意义的。
    Header() Header
    // WriteHeader该方法发送HTTP回复的头域和状态码。
    // 如果没有被显式调用，第一次调用Write时会触发隐式调用WriteHeader(http.StatusOK)
    // WriterHeader的显式调用主要用于发送错误码。
    WriteHeader(int)
    // Write向连接中写入作为HTTP的一部分回复的数据。
    // 如果被调用时还未调用WriteHeader，本方法会先调用WriteHeader(http.StatusOK)
    // 如果Header中没有"Content-Type"键，
    // 本方法会使用包函数DetectContentType检查数据的前512字节，将返回值作为该键的值。
    Write([]byte) (int, error)
}
```
ResponseWriter接口被HTTP处理器用于构造HTTP回复。
### type Flusher
```go
type Flusher interface {
    // Flush将缓冲中的所有数据发送到客户端
    Flush()
}
```
HTTP处理器ResponseWriter接口参数的下层如果实现了Flusher接口，可以让HTTP处理器将缓冲中的数据发送到客户端。

注意：即使ResponseWriter接口的下层支持Flush方法，如果客户端是通过HTTP代理连接的，缓冲中的数据也可能直到回复完毕才被传输到客户端。
### type CloseNotifier
```go
type CloseNotifier interface {
    // CloseNotify返回一个通道，该通道会在客户端连接丢失时接收到唯一的值
    CloseNotify() <-chan bool
}
```
HTTP处理器ResponseWriter接口参数的下层如果实现了CloseNotifier接口，可以让用户检测下层的连接是否停止。如果客户端在回复准备好之前关闭了连接，该机制可以用于取消服务端耗时较长的操作。
### type Hijacker
```go
type Hijacker interface {
    // Hijack让调用者接管连接，返回连接和关联到该连接的一个缓冲读写器。
    // 调用本方法后，HTTP服务端将不再对连接进行任何操作，
    // 调用者有责任管理、关闭返回的连接。
    Hijack() (net.Conn, *bufio.ReadWriter, error)
}
```
HTTP处理器ResponseWriter接口参数的下层如果实现了Hijacker接口，可以让HTTP处理器接管该连接。
### type RoundTripper
```go
type RoundTripper interface {
    // RoundTrip执行单次HTTP事务，接收并发挥请求req的回复。
    // RoundTrip不应试图解析/修改得到的回复。
    // 尤其要注意，只要RoundTrip获得了一个回复，不管该回复的HTTP状态码如何，
    // 它必须将返回值err设置为nil。
    // 非nil的返回值err应该留给获取回复失败的情况。
    // 类似的，RoundTrip不能试图管理高层次的细节，如重定向、认证、cookie。
    //
    // 除了从请求的主体读取并关闭主体之外，RoundTrip不应修改请求，包括（请求的）错误。
    // RoundTrip函数接收的请求的URL和Header字段可以保证是（被）初始化了的。
    RoundTrip(*Request) (*Response, error)
}
```
RoundTripper接口是具有执行单次HTTP事务的能力（接收指定请求的回复）的接口。

RoundTripper接口的类型必须可以安全的被多线程同时使用。

### type Transport
```go
type Transport struct {
    // Proxy指定一个对给定请求返回代理的函数。
    // 如果该函数返回了非nil的错误值，请求的执行就会中断并返回该错误。
    // 如果Proxy为nil或返回nil的*URL置，将不使用代理。
    Proxy func(*Request) (*url.URL, error)
    // Dial指定创建TCP连接的拨号函数。如果Dial为nil，会使用net.Dial。
    Dial func(network, addr string) (net.Conn, error)
    // TLSClientConfig指定用于tls.Client的TLS配置信息。
    // 如果该字段为nil，会使用默认的配置信息。
    TLSClientConfig *tls.Config
    // TLSHandshakeTimeout指定等待TLS握手完成的最长时间。零值表示不设置超时。
    TLSHandshakeTimeout time.Duration
    // 如果DisableKeepAlives为真，会禁止不同HTTP请求之间TCP连接的重用。
    DisableKeepAlives bool
    // 如果DisableCompression为真，会禁止Transport在请求中没有Accept-Encoding头时，
    // 主动添加"Accept-Encoding: gzip"头，以获取压缩数据。
    // 如果Transport自己请求gzip并得到了压缩后的回复，它会主动解压缩回复的主体。
    // 但如果用户显式的请求gzip压缩数据，Transport是不会主动解压缩的。
    DisableCompression bool
    // 如果MaxIdleConnsPerHost!=0，会控制每个主机下的最大闲置连接。
    // 如果MaxIdleConnsPerHost==0，会使用DefaultMaxIdleConnsPerHost。
    MaxIdleConnsPerHost int
    // ResponseHeaderTimeout指定在发送完请求（包括其可能的主体）之后，
    // 等待接收服务端的回复的头域的最大时间。零值表示不设置超时。
    // 该时间不包括获取回复主体的时间。
    ResponseHeaderTimeout time.Duration
    // 内含隐藏或非导出字段
}
```
Transport类型实现了RoundTripper接口，支持http、https和http/https代理。Transport类型可以缓存连接以在未来重用。
```go
var DefaultTransport RoundTripper = &Transport{
    Proxy: ProxyFromEnvironment,
    Dial: (&net.Dialer{
        Timeout:   30 * time.Second,
        KeepAlive: 30 * time.Second,
    }).Dial,
    TLSHandshakeTimeout: 10 * time.Second,
}
```

DefaultTransport是被包变量DefaultClient使用的默认RoundTripper接口。它会根据需要创建网络连接，并缓存以便在之后的请求中重用这些连接。它使用环境变量$HTTP_PROXY和$NO_PROXY（或$http_proxy和$no_proxy）指定的HTTP代理。
#### func (t *Transport) RegisterProtocol(scheme string, rt RoundTripper)
RegisterProtocol注册一个新的名为scheme的协议。t会将使用scheme协议的请求转交给rt。rt有责任模拟HTTP请求的语义。

RegisterProtocol可以被其他包用于提供"ftp"或"file"等协议的实现。
#### func (t *Transport) RoundTrip(req *Request) (resp *Response, err error)
RoundTrip方法实现了RoundTripper接口。

高层次的HTTP客户端支持（如管理cookie和重定向）请参见Get、Post等函数和Client类型。
#### func (t *Transport) CloseIdleConnections()
CloseIdleConnections关闭所有之前的请求建立但目前处于闲置状态的连接。本方法不会中断正在使用的连接。
#### func (t *Transport) CancelRequest(req *Request)
CancelRequest通过关闭请求所在的连接取消一个执行中的请求。
### type Client
```go
type Client struct {
    // Transport指定执行独立、单次HTTP请求的机制。
    // 如果Transport为nil，则使用DefaultTransport。
    Transport RoundTripper
    // CheckRedirect指定处理重定向的策略。
    // 如果CheckRedirect不为nil，客户端会在执行重定向之前调用本函数字段。
    // 参数req和via是将要执行的请求和已经执行的请求（切片，越新的请求越靠后）。
    // 如果CheckRedirect返回一个错误，本类型的Get方法不会发送请求req，
    // 而是返回之前得到的最后一个回复和该错误。（包装进url.Error类型里）
    //
    // 如果CheckRedirect为nil，会采用默认策略：连续10此请求后停止。
    CheckRedirect func(req *Request, via []*Request) error
    // Jar指定cookie管理器。
    // 如果Jar为nil，请求中不会发送cookie，回复中的cookie会被忽略。
    Jar CookieJar
    // Timeout指定本类型的值执行请求的时间限制。
    // 该超时限制包括连接时间、重定向和读取回复主体的时间。
    // 计时器会在Head、Get、Post或Do方法返回后继续运作并在超时后中断回复主体的读取。
    //
    // Timeout为零值表示不设置超时。
    //
    // Client实例的Transport字段必须支持CancelRequest方法，
    // 否则Client会在试图用Head、Get、Post或Do方法执行请求时返回错误。
    // 本类型的Transport字段默认值（DefaultTransport）支持CancelRequest方法。
    Timeout time.Duration
}
```
Client类型代表HTTP客户端。它的零值（DefaultClient）是一个可用的使用DefaultTransport的客户端。

Client的Transport字段一般会含有内部状态（缓存TCP连接），因此Client类型值应尽量被重用而不是每次需要都创建新的。Client类型值可以安全的被多个go程同时使用。

Client类型的层次比RoundTripper接口（如Transport）高，还会管理HTTP的cookie和重定向等细节。

#### func (c *Client) Do(req *Request) (resp *Response, err error)
Do方法发送请求，返回HTTP回复。它会遵守客户端c设置的策略（如重定向、cookie、认证）。

如果客户端的策略（如重定向）返回错误或存在HTTP协议错误时，本方法将返回该错误；如果回应的状态码不是2xx，本方法并不会返回错误。

如果返回值err为nil，resp.Body总是非nil的，调用者应该在读取完resp.Body后关闭它。如果返回值resp的主体未关闭，c下层的RoundTripper接口（一般为Transport类型）可能无法重用resp主体下层保持的TCP连接去执行之后的请求。

请求的主体，如果非nil，会在执行后被c.Transport关闭，即使出现错误。

一般应使用Get、Post或PostForm方法代替Do方法。
#### func (c *Client) Head(url string) (resp *Response, err error)
Head向指定的URL发出一个HEAD请求，如果回应的状态码如下，Head会在调用c.CheckRedirect后执行重定向：

```go
301 (Moved Permanently)
302 (Found)
303 (See Other)
307 (Temporary Redirect)
```

#### func (c *Client) Get(url string) (resp *Response, err error)
Get向指定的URL发出一个GET请求，如果回应的状态码如下，Get会在调用c.CheckRedirect后执行重定向：
```go
301 (Moved Permanently)
302 (Found)
303 (See Other)
307 (Temporary Redirect)
```
如果c.CheckRedirect执行失败或存在HTTP协议错误时，本方法将返回该错误；如果回应的状态码不是2xx，本方法并不会返回错误。如果返回值err为nil，resp.Body总是非nil的，调用者应该在读取完resp.Body后关闭它。
#### func (c *Client) Post(url string, bodyType string, body io.Reader) (resp *Response, err error)
Post向指定的URL发出一个POST请求。bodyType为POST数据的类型， body为POST数据，作为请求的主体。如果参数body实现了io.Closer接口，它会在发送请求后被关闭。调用者有责任在读取完返回值resp的主体后关闭它。
#### func (c *Client) PostForm(url string, data url.Values) (resp *Response, err error)
PostForm向指定的URL发出一个POST请求，url.Values类型的data会被编码为请求的主体。POST数据的类型一般会设为"application/x-www-form-urlencoded"。如果返回值err为nil，resp.Body总是非nil的，调用者应该在读取完resp.Body后关闭它
### type Handler
```go
type Handler interface {
    ServeHTTP(ResponseWriter, *Request)
}
```
实现了Handler接口的对象可以注册到HTTP服务端，为特定的路径及其子树提供服务。

ServeHTTP应该将回复的头域和数据写入ResponseWriter接口然后返回。返回标志着该请求已经结束，HTTP服务端可以转移向该连接上的下一个请求。
#### func NotFoundHandler() Handler
NotFoundHandler返回一个简单的请求处理器，该处理器会对每个请求都回复"404 page not found"。
#### func RedirectHandler(url string, code int) Handler
RedirectHandler返回一个请求处理器，该处理器会对每个请求都使用状态码code重定向到网址url。
#### func TimeoutHandler(h Handler, dt time.Duration, msg string) Handler
TimeoutHandler返回一个采用指定时间限制的请求处理器。

返回的Handler会调用h.ServeHTTP去处理每个请求，但如果某一次调用耗时超过了时间限制，该处理器会回复请求状态码503 Service Unavailable，并将msg作为回复的主体（如果msg为空字符串，将发送一个合理的默认信息）。在超时后，h对它的ResponseWriter接口参数的写入操作会返回ErrHandlerTimeout。
#### func StripPrefix(prefix string, h Handler) Handler
StripPrefix返回一个处理器，该处理器会将请求的URL.Path字段中给定前缀prefix去除后再交由h处理。StripPrefix会向URL.Path字段中没有给定前缀的请求回复404 page not found。
### type HandlerFunc
HandlerFunc type是一个适配器，通过类型转换让我们可以将普通的函数作为HTTP处理器使用。如果f是一个具有适当签名的函数，HandlerFunc(f)通过调用f实现了Handler接口。
#### func (f HandlerFunc) ServeHTTP(w ResponseWriter, r *Request)
ServeHTTP方法会调用f(w, r)
### type ServeMux
ServeMux类型是HTTP请求的多路转接器。它会将每一个接收的请求的URL与一个注册模式的列表进行匹配，并调用和URL最匹配的模式的处理器。

模式是固定的、由根开始的路径，如"/favicon.ico"，或由根开始的子树，如"/images/"（注意结尾的斜杠）。较长的模式优先于较短的模式，因此如果模式"/images/"和"/images/thumbnails/"都注册了处理器，后一个处理器会用于路径以"/images/thumbnails/"开始的请求，前一个处理器会接收到其余的路径在"/images/"子树下的请求。

注意，因为以斜杠结尾的模式代表一个由根开始的子树，模式"/"会匹配所有的未被其他注册的模式匹配的路径，而不仅仅是路径"/"。

模式也能（可选地）以主机名开始，表示只匹配该主机上的路径。指定主机的模式优先于一般的模式，因此一个注册了两个模式"/codesearch"和"codeserch.google.com/“ 的处理其不会接管目标为[http://www.google.com/](http://www.google.com) 的请求。

ServeMux还会注意到请求的URL路径的无害化，将任何路径中包含"."或".."元素的请求重定向到等价的没有这两种元素的URL。（参见path.Clean函数）
#### func NewServeMux() *ServeMux
NewServeMux创建并返回一个新的*ServeMux
#### func (mux *ServeMux) Handle(pattern string, handler Handler)
Handle注册HTTP处理器handler和对应的模式pattern。如果该模式已经注册有一个处理器，Handle会panic。
#### func (mux *ServeMux) HandleFunc(pattern string, handler func(ResponseWriter, *Request))
HandleFunc注册一个处理器函数handler和对应的模式pattern。
#### func (mux *ServeMux) Handler(r *Request) (h Handler, pattern string)
Handler根据r.Method、r.Host和r.URL.Path等数据，返回将用于处理该请求的HTTP处理器。它总是返回一个非nil的处理器。如果路径不是它的规范格式，将返回内建的用于重定向到等价的规范路径的处理器。

Handler也会返回匹配该请求的的已注册模式；在内建重定向处理器的情况下，pattern会在重定向后进行匹配。如果没有已注册模式可以应用于该请求，本方法将返回一个内建的"404 page not found"处理器和一个空字符串模式。
#### func (mux *ServeMux) ServeHTTP(w ResponseWriter, r *Request)
ServeHTTP将请求派遣到与请求的URL最匹配的模式对应的处理器。
### type Server
```go
type Server struct {
    Addr           string        // 监听的TCP地址，如果为空字符串会使用":http"
    Handler        Handler       // 调用的处理器，如为nil会调用http.DefaultServeMux
    ReadTimeout    time.Duration // 请求的读取操作在超时前的最大持续时间
    WriteTimeout   time.Duration // 回复的写入操作在超时前的最大持续时间
    MaxHeaderBytes int           // 请求的头域最大长度，如为0则用DefaultMaxHeaderBytes
    TLSConfig      *tls.Config   // 可选的TLS配置，用于ListenAndServeTLS方法
    // TLSNextProto（可选地）指定一个函数来在一个NPN型协议升级出现时接管TLS连接的所有权。
    // 映射的键为商谈的协议名；映射的值为函数，该函数的Handler参数应处理HTTP请求，
    // 并且初始化Handler.ServeHTTP的*Request参数的TLS和RemoteAddr字段（如果未设置）。
    // 连接在函数返回时会自动关闭。
    TLSNextProto map[string]func(*Server, *tls.Conn, Handler)
    // ConnState字段指定一个可选的回调函数，该函数会在一个与客户端的连接改变状态时被调用。
    // 参见ConnState类型和相关常数获取细节。
    ConnState func(net.Conn, ConnState)
    // ErrorLog指定一个可选的日志记录器，用于记录接收连接时的错误和处理器不正常的行为。
    // 如果本字段为nil，日志会通过log包的标准日志记录器写入os.Stderr。
    ErrorLog *log.Logger
    // 内含隐藏或非导出字段
}
```
Server类型定义了运行HTTP服务端的参数。Server的零值是合法的配置。
#### func (s *Server) SetKeepAlivesEnabled(v bool)
SetKeepAlivesEnabled控制是否允许HTTP闲置连接重用（keep-alive）功能。默认该功能总是被启用的。只有资源非常紧张的环境或者服务端在关闭进程中时，才应该关闭该功能。
#### func (srv *Server) Serve(l net.Listener) error

Serve会接手监听器l收到的每一个连接，并为每一个连接创建一个新的服务go程。该go程会读取请求，然后调用srv.Handler回复请求。
#### func (srv *Server) ListenAndServe() error
ListenAndServe监听srv.Addr指定的TCP地址，并且会调用Serve方法接收到的连接。如果srv.Addr为空字符串，会使用":http"。
#### func (srv *Server) ListenAndServeTLS(certFile, keyFile string) error
ListenAndServeTLS监听srv.Addr确定的TCP地址，并且会调用Serve方法处理接收到的连接。必须提供证书文件和对应的私钥文件。如果证书是由权威机构签发的，certFile参数必须是顺序串联的服务端证书和CA证书。如果srv.Addr为空字符串，会使用":https"。
### type File

```go
type File interface {
    io.Closer
    io.Reader
    Readdir(count int) ([]os.FileInfo, error)
    Seek(offset int64, whence int) (int64, error)
    Stat() (os.FileInfo, error)
}
```

File是被FileSystem接口的Open方法返回的接口类型，可以被FileServer等函数用于文件访问服务。

该接口的方法的行为应该和*os.File类型的同名方法相同。

### type FileSystem

```go
type FileSystem interface {
    Open(name string) (File, error)
}
```

FileSystem接口实现了对一系列命名文件的访问。文件路径的分隔符为'/'，不管主机操作系统的惯例如何。

### type Dir 

```
type Dir string
```

Dir使用限制到指定目录树的本地文件系统实现了http.FileSystem接口。空Dir被视为"."，即代表当前目录。

#### func (d Dir) Open(name string) (File, error)
#### func NewFileTransport(fs FileSystem) RoundTripper

NewFileTransport返回一个RoundTripper接口，使用FileSystem接口fs提供文件访问服务。 返回的RoundTripper接口会忽略接收的请求的URL主机及其他绝大多数属性。

NewFileTransport函数的典型使用情况是给Transport类型的值注册"file"协议，如下所示：

```go
t := &http.Transport{}
t.RegisterProtocol("file", http.NewFileTransport(http.Dir("/")))
c := &http.Client{Transport: t}
res, err := c.Get("file:///etc/passwd")
...
```

#### func FileServer(root FileSystem) Handler

FileServer返回一个使用FileSystem接口root提供文件访问服务的HTTP处理器。要使用操作系统的FileSystem接口实现，可使用http.Dir：

```go
http.Handle("/", http.FileServer(http.Dir("/tmp")))
```

#### func ProxyURL(fixedURL *url.URL) func(*Request) (*url.URL, error)

ProxyURL返回一个代理函数（用于Transport类型），该函数总是返回同一个URL。

#### func ProxyFromEnvironment(req *Request) (*url.URL, error)
ProxyFromEnvironment使用环境变量$HTTP_PROXY和$NO_PROXY(或$http_proxy和$no_proxy)的配置返回用于req的代理。如果代理环境不合法将返回错误；如果环境未设定代理或者给定的request不应使用代理时，将返回(nil, nil)；如果req.URL.Host字段是"localhost"（可以有端口号，也可以没有），也会返回(nil, nil)。

#### func SetCookie(w ResponseWriter, cookie *Cookie)

SetCookie在w的头域中添加Set-Cookie头，该HTTP头的值为cookie。

#### func Redirect(w ResponseWriter, r *Request, urlStr string, code int)

Redirect回复请求一个重定向地址urlStr和状态码code。该重定向地址可以是相对于请求r的相对地址。

#### func NotFound(w ResponseWriter, r *Request)

NotFound回复请求404状态码（not found：目标未发现）。

#### func Error(w ResponseWriter, error string, code int)

Error使用指定的错误信息和状态码回复请求，将数据写入w。错误信息必须是明文。

#### func ServeContent(w ResponseWriter, req *Request, name string, modtime time.Time, content io.ReadSeeker)

ServeContent使用提供的ReadSeeker的内容回复请求。ServeContent比起io.Copy函数的主要优点，是可以处理范围类请求（只要一部分内容）、设置MIME类型，处理If-Modified-Since请求。

如果未设定回复的Content-Type头，本函数首先会尝试从name的文件扩展名推断数据类型；如果失败，会用读取content的第1块数据并提供给DetectContentType推断类型；之后会设置Content-Type头。参数name不会用于别的地方，甚至于它可以是空字符串，也永远不会发送到回复里。

如果modtime不是Time零值，函数会在回复的头域里设置Last-Modified头。如果请求的头域包含If-Modified-Since头，本函数会使用modtime参数来确定是否应该发送内容。如果调用者设置了w的ETag头，ServeContent会使用它处理包含If-Range头和If-None-Match头的请求。

参数content的Seek方法必须有效：函数使用Seek来确定它的大小。

注意：本包File接口和*os.File类型都实现了io.ReadSeeker接口。

#### func ServeFile(w ResponseWriter, r *Request, name string)

ServeFile回复请求name指定的文件或者目录的内容。

#### func MaxBytesReader(w ResponseWriter, r io.ReadCloser, n int64) io.ReadCloser

MaxBytesReader类似io.LimitReader，但它是用来限制接收到的请求的Body的大小的。不同于io.LimitReader，本函数返回一个ReadCloser，返回值的Read方法在读取的数据超过大小限制时会返回非EOF错误，其Close方法会关闭下层的io.ReadCloser接口r。

MaxBytesReader预防客户端因为意外或者蓄意发送的“大”请求，以避免尺寸过大的请求浪费服务端资源。

#### func Head(url string) (resp *Response, err error)

Head向指定的URL发出一个HEAD请求，如果回应的状态码如下，Head会在调用c.CheckRedirect后执行重定向：

```go
301 (Moved Permanently)
302 (Found)
303 (See Other)
307 (Temporary Redirect)
```

Head是对包变量DefaultClient的Head方法的包装。

#### func Get(url string) (resp *Response, err error)

Get向指定的URL发出一个GET请求，如果回应的状态码如下，Get会在调用c.CheckRedirect后执行重定向：

```go
301 (Moved Permanently)
302 (Found)
303 (See Other)
307 (Temporary Redirect)
```

如果c.CheckRedirect执行失败或存在HTTP协议错误时，本方法将返回该错误；如果回应的状态码不是2xx，本方法并不会返回错误。如果返回值err为nil，resp.Body总是非nil的，调用者应该在读取完resp.Body后关闭它。

Get是对包变量DefaultClient的Get方法的包装。

#### func Post(url string, bodyType string, body io.Reader) (resp *Response, err error)

Post向指定的URL发出一个POST请求。bodyType为POST数据的类型， body为POST数据，作为请求的主体。如果参数body实现了io.Closer接口，它会在发送请求后被关闭。调用者有责任在读取完返回值resp的主体后关闭它。

Post是对包变量DefaultClient的Post方法的包装。

#### func PostForm(url string, data url.Values) (resp *Response, err error)

PostForm向指定的URL发出一个POST请求，url.Values类型的data会被编码为请求的主体。如果返回值err为nil，resp.Body总是非nil的，调用者应该在读取完resp.Body后关闭它。

PostForm是对包变量DefaultClient的PostForm方法的包装。

#### func Handle(pattern string, handler Handler)

Handle注册HTTP处理器handler和对应的模式pattern（注册到DefaultServeMux）。如果该模式已经注册有一个处理器，Handle会panic。ServeMux的文档解释了模式的匹配机制。

#### func HandleFunc(pattern string, handler func(ResponseWriter, *Request))

HandleFunc注册一个处理器函数handler和对应的模式pattern（注册到DefaultServeMux）。ServeMux的文档解释了模式的匹配机制。

#### func Serve(l net.Listener, handler Handler) error

Serve会接手监听器l收到的每一个连接，并为每一个连接创建一个新的服务go程。该go程会读取请求，然后调用handler回复请求。handler参数一般会设为nil，此时会使用DefaultServeMux。

#### func ListenAndServe(addr string, handler Handler) error

ListenAndServe监听TCP地址addr，并且会使用handler参数调用Serve函数处理接收到的连接。handler参数一般会设为nil，此时会使用DefaultServeMux。

一个简单的服务端例子：

```go
package main
import (
	"io"
	"net/http"
	"log"
)
// hello world, the web server
func HelloServer(w http.ResponseWriter, req *http.Request) {
	io.WriteString(w, "hello, world!\n")
}
func main() {
	http.HandleFunc("/hello", HelloServer)
	err := http.ListenAndServe(":12345", nil)
	if err != nil {
		log.Fatal("ListenAndServe: ", err)
	}
}
```

#### func ListenAndServeTLS(addr string, certFile string, keyFile string, handler Handler) error

ListenAndServeTLS函数和ListenAndServe函数的行为基本一致，除了它期望HTTPS连接之外。此外，必须提供证书文件和对应的私钥文件。如果证书是由权威机构签发的，certFile参数必须是顺序串联的服务端证书和CA证书。如果srv.Addr为空字符串，会使用":https"。

一个简单的服务端例子：

```go
import (
	"log"
	"net/http"
)
func handler(w http.ResponseWriter, req *http.Request) {
	w.Header().Set("Content-Type", "text/plain")
	w.Write([]byte("This is an example server.\n"))
}
func main() {
	http.HandleFunc("/", handler)
	log.Printf("About to listen on 10443. Go to https://127.0.0.1:10443/")
	err := http.ListenAndServeTLS(":10443", "cert.pem", "key.pem", nil)
	if err != nil {
		log.Fatal(err)
	}
}
```

