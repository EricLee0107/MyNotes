## Markdown绘图



## 流程图

### 节点类型

#### start

`st=>start: 开始`

![image-20201110172446730](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20201110172446730.png)





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

![image-20201110172528189](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20201110172528189.png)

#### 







```flow
st=>start: 开始
e=>end: 结束
op1=>operation: 操作节点
sub1=>subroutine: 子程序节点
cond=>condition: 条件节点
Yes or No?
io=>inputoutput: 输入输出节点
para=>parallel: 平行节点

st->op1->cond
cond(yes)->io->e
cond(no)->para
para(path1, bottom)->sub1(right)->op1
para(path2, top)->op1
```

