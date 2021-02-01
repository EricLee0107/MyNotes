# go tool vet

## 版本

vet version go1.14.2

## 注册的分析器

| 分析器名称   | 描述                                              |
| ------------ | ------------------------------------------------- |
| asmdecl      | 报告程序集文件与Go生命之间的不匹配                |
| assign       | 检查赋值语句                                      |
| atomic       | 检查代码中对代码包sync/atomic的使用是否正确       |
| bools        | 检查涉及到布尔操作符的常见错误                    |
| buildtag     | 检查编译标签是否格式良好，位置正确                |
| cgocall      | 检查一些违反cgo指针传递规则的行为                 |
| composites   | 检查符合结构实例的初始化代码                      |
| copylocks    | 检查通过值错误传递的锁                            |
| errorsas     | 报告将非指针或非错误值传递给errors.As             |
| httpresponse | 检查使用HTTP响应的错误                            |
| loopclosure  | 检查嵌套函数中对循环变量的引用                    |
| lostcancel   | 检查由context返回的取消函数                       |
| nilfunc      | 检查函数和nil之间无用的比较                       |
| printf       | 检查打印函数的格式化字符串和参数的一致性          |
| shift        | 检查是否有等于或超过整数宽度的移位                |
| stdmethods   | 检查知名接口的方法签名                            |
| structtag    | 检查struct字段标签后是否符合reflect.StructTag.Get |
| tests        | 检查测试和示例的常见错误用法                      |
| unmarshal    | 检查将非指针或非接口值传递给unmarshal             |
| unreachable  | 检查不可到达的代码                                |
| unsafeptr    | 检查uintptr到unsafe.Pointer的无效转换             |
| unusedresult | 检查对某些函数调用的未使用结果                    |



## 标记

### 标记总览

| 标记名称            | 标记描述                                                     |
| ------------------- | ------------------------------------------------------------ |
| -V                  | 打印版本并退出                                               |
| -asmdecl            | 使用asmdecl分析器                                            |
| -assign             | 使用assign分析器                                             |
| -atomic             | 使用atomic分析器                                             |
| -bools              | 使用bool分析器                                               |
| -buildtag           | 使用buildtag分析器                                           |
| -c int              | 用多少行显示违规的行（默认为-1）                             |
| -cgocall            | 使用cgocall分析器                                            |
| -composites         | 检查复合结构实例的初始化代码。默认值为false。                |
| -compositeWhiteList | 是否使用复合结构检查的白名单。仅供测试使用                   |
| -copylocks          | 使用copulocks分析器                                          |
| -errorsas           | 使用errorsas分析器                                           |
| -flags              | 在json中打印分析器标志                                       |
| -httpresponse       | 使用httpresopnse分析器                                       |
| -json               | 使用json输出                                                 |
| -loopclosure        | 使用loopclosure分析器                                        |
| -lostcancel         | 使用lostcancel分析器                                         |
| -methods            | 检查那些拥有标准命名的方法的签名。                           |
| -nilfunc            | 使用nilfunc分析器                                            |
| -printf             | 检查代码中对打印函数的使用是否正确。                         |
| -printfuncs         | 需要检查的代码中使用的打印函数的名称的列表，多个函数名称之间用英文半角逗号分隔。默认值为空字符串。 |
| -rangeloops         | 检查代码中对在```range```语句块中迭代赋值的变量的使用是否正确。。 |
| -shift              | 使用shift分析器                                              |
| -stdmethods         | 使用stdmethods分析器                                         |
| -structtags         | 使用structtags分析器                                         |
| -tests              | 使用tests分析器                                              |
| -unmarshal          | 使用unmarshal分析器                                          |
| -unreachable        | 查找并报告不可到达的代码。默认值为false。                    |
| -unsafeptr          | 使用unsafeptr分析器                                          |
| -unusedresult       | 使用 unusedresult分析器                                      |



### 标记详解与举例

