[TOC]

## Markdown绘图

## 流程图(Flow)

### 节点定义

定义格式： `节点名称=>节点类型:节点显示文本。

节点名称可以随意起名，节点显示文本可以是任意字符，或者为空（使用默认值）

#### 节点类型

##### start

`st=>start: 开始`

![image-20201110172446730](https://eric-typora-img.oss-cn-beijing.aliyuncs.com/typora/202011/13/114621-230417.png)





#### end

`e=>end: 结束`

![image-20201110172457412](https://eric-typora-img.oss-cn-beijing.aliyuncs.com/typora/202011/10/172505-982105.png)

#### operation

`op1=>operation: 操作节点`



![image-20201110172510292](https://eric-typora-img.oss-cn-beijing.aliyuncs.com/typora/202011/10/172511-340120.png)

#### input/output

`io=>inputoutput: 输入输出节点`

![image-20201110172516757](https://eric-typora-img.oss-cn-beijing.aliyuncs.com/typora/202011/10/172517-28437.png)

#### subroutine

`sub1=>subroutine: 子程序节点`

![image-20201110172521445](https://eric-typora-img.oss-cn-beijing.aliyuncs.com/typora/202011/10/172522-480884.png)

#### condition

`cond=>condition: 条件节点 Yes or No?`

![image-20201110172524852](https://eric-typora-img.oss-cn-beijing.aliyuncs.com/typora/202011/10/172525-921917.png)

#### parallel

`para=>parallel: 平行节点`

![image-20201110172528189](https://eric-typora-img.oss-cn-beijing.aliyuncs.com/typora/202011/13/114629-64297.png)



### 节点连接

**格式：**

一般节点连接：

​	节点->节点

条件判断节点连接：

​	条件节点（yes)->正确应答节点

​	条件节点（no)->错误应答节点

注： 可以通过在一个字符串中指定所有连接，也可以将连接分到多个字符串中，比如

```markdown
节点1->节点2->节点3
```

与下面这种表示的结果是相同的：

```markdown
节点1->节点2
节点2->节点3
```



## UML序列图

### 1. 设置标题

```markdown
title:序列图标题
```

效果：

```sequence
title:序列图标题
```



### 2. 设置参与者

```markdown
title:序列图标题
participant 参与者A
participant 参与者B
participant 参与者C
participant 参与者D
```

效果：

```sequence
title:序列图标题
participant 参与者A
participant 参与者B
participant 参与者C
participant 参与者D
```



### 3. 设置节点

- 左侧note： note left of acotor： message
- 右侧note： note right of actor: message,
- 覆盖note： note over actor:message

```markdown
title:序列图标题
participant 参与者A
participant 参与者B
participant 参与者C
participant 参与者D
note left of 参与者A: 左侧note
note right of 参与者D: 右侧note
note over 参与者C: 覆盖note
note over 参与者B,参与者C: 覆盖B-C note
note over 参与者A,参与者D: 覆盖A-D note
```

效果：

```sequence
title:序列图标题
participant 参与者A
participant 参与者B
participant 参与者C
participant 参与者D
note left of 参与者A: 左侧note
note right of 参与者D: 右侧note
note over 参与者C: 覆盖note
note over 参与者B,参与者C: 覆盖B-C note
note over 参与者A,参与者D: 覆盖A-D note


```





### 4. 设置会话

- 实线实箭头： actor->actor: message
- 虚线实箭头： actor–>actor:message
- 实线虚箭头： actor->>actor:message
- 虚线虚箭头： actor–>>actor:message

```markdown
title:序列图标题
participant 参与者A
participant 参与者B
participant 参与者C
participant 参与者D
note left of 参与者A: 左侧note
note right of 参与者D: 右侧note
note over 参与者C: 覆盖note
note over 参与者B,参与者C: 覆盖B-C note
note over 参与者A,参与者D: 覆盖A-D note
参与者A->参与者A:自言自语
参与者B->参与者D:实线实箭头
参与者C-->参与者A:虚线实箭头
参与者A->>参与者D:实线虚箭头
参与者D-->>参与者A:虚线虚箭头
```



```sequence
title:序列图标题
participant 参与者A
participant 参与者B
participant 参与者C
participant 参与者D
note left of 参与者A: 左侧note
note right of 参与者D: 右侧note
note over 参与者C: 覆盖note
note over 参与者B,参与者C: 覆盖B-C note
note over 参与者A,参与者D: 覆盖A-D note
参与者A->参与者A:自言自语
参与者B->参与者D:实线实箭头
参与者C-->参与者A:虚线实箭头
参与者A->>参与者D:实线虚箭头
参与者D-->>参与者A:虚线虚箭头
```

