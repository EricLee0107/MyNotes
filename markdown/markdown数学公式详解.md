# Markdown数学公式详解

[TOC]



## 行内和独行



1. 行内公式：将公式插入到本行内，符号：`$公式内容$`，如：$xyz$
2. 独行公式：将公式插入到新的一行内，并且居中，符号：`$$公式内容$$`，如：$$xyz$$



## 上标、下表与组合



- 上标：`^`	举例：`$x^2$` -> $x^2$

- 下标：`_`  举例：`$x_1$` -> $x_1$

- 组合：`{}`  举例：`${H}_2{O}$` -> ${H}_2{O}$





## 占位符



- 两个quad空格，符号：`\qquad`，如：$x \qquad y$
- quad空格，符号：`\quad`，如：$x \quad y$
- 大空格，符号`\`，如：$x \  y$
- 中空格，符号`\:`，如：$x : y$
- 小空格，符号`\,`，如：$x , y$
- 没有空格，符号``，如：$xy$`
- `紧贴，符号`\!`，如：$x ! y$



## 定界符与组合



- 括号，符号：`（）\big(\big) \Big(\Big) \bigg(\bigg) \Bigg(\Bigg)`，如：$（）\big(\big) \Big(\Big) \bigg(\bigg) \Bigg(\Bigg)$
- 中括号，符号：`[]`，如：$[x+y]$
- 大括号，符号：`\{ \}`，如：${x+y}$
- 自适应括号，符号：`\left \right`，如：$\left(x\right)$，$\left(x{yz}\right)$
- 组合公式，符号：`{上位公式 \choose 下位公式}`，如：${n+1 \choose k}={n \choose k}+{n \choose k-1}$
- 组合公式，符号：`{上位公式 \atop 下位公式}`，如：$\sum_{k_0,k_1,\ldots>0 \atop k_0+k_1+\cdots=n}A_{k_0}A_{k_1}\cdots$



## 四则运算



|      数学符号       |    含义    |      Markdown       | 举例(markdown代码 -> 显示效果)        |
| :-----------------: | :--------: | :-----------------: | ------------------------------------- |
|         $+$         | 加法运算符 |         `+`         | `$x+y=z$` -> $x+y=z$                  |
|         $-$         | 减法运算符 |         `-`         | `$x-y=z$`   ->   $x-y=z$              |
|      $\times$       | 乘法运算符 |      `\times`       | `$x \times y=z$` -> $x \times y=z$    |
|       $\div$        | 除法运算符 |       `\div`        | `$x \div y = z$` -> $x \div y = z$    |
|        $\pm$        | 加减运算符 |        `\pm`        | `$x \pm y=z$` -> $x \pm y=z$          |
|        $\mp$        | 减加运算符 |        `\mp`        | `$x \mp y = z$` -> $x \mp y = z$      |
|       $\cdot$       | 点乘运算符 |       `\cdot`       | `$x \cdot y = z$` -> $x \cdot y = z$  |
|       $\ast$        | 星乘运算符 |       `\ast`        | `$x \ast y = z$` -> $x \ast y = z$    |
|         $/$         | 斜法运算符 |         `/`         | `$x/y=z$` -> $x/y=z$                  |
| $\frac{分子}{分母}$ |  分式表示  | `\frac{分子}{分母}` | `$\frac{1}{2}$` -> $\frac{1}{2}$      |
| ${分子}\over{分母}$ |  分式表示  | `{分子}\over{分母}` | `${17}\over{83}$` - > ${17}\over{83}$ |
|        $||$         |  绝对值符  |        `||`         | `$|x+y|$` - > $|x+y|$                 |

<div STYLE="page-break-after: always;"></div>

## 逻辑运算



|  数学符号  |    含义    |  Markdown  |     举例（Markdown内容 -> 公式效果）     |
| :--------: | :--------: | :--------: | :--------------------------------------: |
|    $=$     |    等于    |    `=`     |          `$x+y=z$`  ->  $x+y=z$          |
|    $>$     |    小于    |    `>`     |          `$x+y>z$`  ->  $x+y>z$          |
|    $<$     |    大于    |    `<`     |          `$x+y<z$`  ->  $x+y<z$          |
|   $\geq$   |  大于等于  |   `\geq`   |     `$x+y \geq z$`  ->  $x+y \geq z$     |
|   $\leq$   |  小于等于  |   `\leq`   |     `$x+y \leq z$`  ->  $x+y \leq z$     |
|   $\neq$   |   不等于   |   `\neq`   |     `$x+y \neq z$`  ->  $x+y \neq z$     |
|  $\ngeq$   | 不大于等于 |  `\ngeq`   |    `$x+y \ngeq z$`  ->  $x+y \ngeq z$    |
| $\not\geq$ | 不大于等于 | `\not\geq` | `$x+y \not\geq z$`  ->  $x+y \not\geq z$ |
|  $\nleq$   | 不小于等于 |  `\nleq`   |    `$x+y \nleq z$`  ->  $x+y \nleq z$    |
| $\not\leq$ | 不小于等于 | `\not\leq` | `$x+y \not\leq z$`  ->  $x+y \not\leq z$ |
| $\approx$  |   约等于   | `\approx`  |  `$x+y \approx z$`  ->  $x+y \approx z$  |
|  $\equiv$  |   恒等于   |  `\equiv`  |   `$x+y \equiv z$`  ->  $x+y \equiv z$   |



<div STYLE="page-break-after: always;"></div>

## 高级运算



| 数学符号                                                     | 含义               | Markdown                      | 举例（Markdown内容 -> 公式效果）                         |
| ------------------------------------------------------------ | ------------------ | ----------------------------- | -------------------------------------------------------- |
| $\overline{求平均的算式}$                                    | 平均数运算         | `\overline{求平均的算式}`     | `$\overline{xyz}$` -> $\overline{xyz}$                   |
| $\sqrt[开方数]{被开方数}$                                    | 开方运算           | `\sqrt[开方数]{被开方数}`     |                                                          |
| $\sqrt {被开平方数}$                                         | 开二次方运算       | `\sqrt{被开方数}`             |                                                          |
| $\log_{x}{y}$                                                | 对数运算           | `\log_{底数}{真数}`           |                                                          |
| $ln {x}$                                                     | 自然对数运算       | `\ln {真数}`                  |                                                          |
| $lg {x}$                                                     | 以10为底的对数运算 | `\lg {真数}`                  |                                                          |
| $\lim{a+b}$                                                  | 极限运算           | `\lim 极限算式`               | $\lim^{x \to \infty}_{y \to 0}{\frac{x}{y}}$             |
| $\sum{求和算式}$                                             | 求和运算（累加）   | `\sum 求和算式`               | `$\sum^{100}_{i=1}$` -> $\sum^{100}_{i=1}$               |
| $\prod 累乘算式$                                             | 累乘运算           | `\prod 累乘算式`              | `$\prod_{n=1}^{99}{x_n}$` -> $\prod_{n=1}^{99}{x_n}$     |
| $\int$                                                       | 积分运算           | `\int 积分算式`               | `$\int^{\infty}_{0}f({x})$` -> $\int^{\infty}_{0}f({x})$ |
| $\partial$                                                   | 微分运算           | `\partial`                    | $\frac{\partial x}{\partial y}$                          |
| $\left[ \begin{matrix} 1 &2 &\cdots &4\\5 &6 &\cdots &8 \\\vdots &\vdots &\ddots &\vdots \\13 &14 &\cdots &16\end{matrix} \right]$ | 矩阵表示           | `\begin{matrix} \end{matrix}` |                                                          |

<div STYLE="page-break-after: always;"></div>

## 集合运算



| 符号          | 含义       | Markdown      | 举例（Markdown内容 -> 公式效果）         |
| ------------- | ---------- | ------------- | ---------------------------------------- |
| $\in$         | 属于       | `\in`         | `$x \in y$` -> $x \in y$                 |
| $\notin$      | 不属于     | `\notin`      | `$x \notin y$` -> $x \notin y$           |
| $\not\in$     | 不属于     | `\not\in`     | `$x \not\in y$` -> $x \not\in y$         |
| $\subset$     | 子集       | `\subset`     | `$x \subset y$` -> $x \subset y$         |
| $\supset$     | 子集       | `\supset`     | `$x \supset y$` -> $x \supset y$         |
| $\subseteq$   | 真子集     | `\subseteq`   | `$x \subseteq y$` -> $x \subseteq y$     |
| $\subsetneq$  | 非真子集   | `\subsetneq`  | `$x \subsetneq y$` -> $x \subsetneq y$   |
| $\supseteq$   | 真子集     | `\supseteq`   | `$x \supseteq y$` -> $x \supseteq y$     |
| $\supsetneq$  | 非真子集   | `\supsetneq`  | `$x \supsetneq y$` -> $x \supsetneq y$   |
| $\not\subset$ | 非子集     | `\not\subset` | `$x \not\subset y$` -> $x \not\subset y$ |
| $\not\supset$ | 非子集     | `\not\supset` | `$x \not\supset y$` -> $x \not\supset y$ |
| $\cup$        | 并集       | `\cup`        | `$x \cup y$` -> $x \cup y$               |
| $\cap$        | 交集       | `\cap`        | `$x \cap y$` -> $x \cap y$               |
| $\setminus$   | 差集       | `\setminus`   | `$x \setminus y$` -> $x \setminus y$     |
| $\bigodot$    | 同或       | `\bigodot`    | `$x \bigodot y$` -> $x \bigodot y$       |
| $\bigotimes$  | 同与       | `\bigotimes`  | `$x \bigotimes y$` -> $x \bigotimes y$   |
| $\mathbb{R}$  | 实数集合   | `\mathbb{R}`  |                                          |
| $\mathbb{Z}$  | 自然数集合 | `\mathbb{Z}   |                                          |
| $\emptyset$   | 空集       | `\emptyset`   |                                          |

<div STYLE="page-break-after: always;"></div>

## 三角函数



|   符号    |    含义    | Markdown  |
| :-------: | :--------: | :-------: |
|  $\sin$   |  正弦函数  |  `\sin`   |
|  $\cos$   |  余弦函数  |  `\cos`   |
|  $\tan$   |  正切函数  |  `\tan`   |
|  $\cot$   |  余切函数  |  `\cot`   |
|  $\sec$   |  正割函数  |  `\sec`   |
|  $\csc$   |  余割函数  |  `\csc`   |
| $\arcsin$ | 反正弦函数 | `\arcsin` |
| $\arccos$ | 反余弦函数 | `\arccos` |
| $\arctan$ | 反正切函数 | `\arctan` |



## 箭头



|     符号      |  含义  |   Markdown    |
| :-----------: | :----: | :-----------: |
|  $\uparrow$   | 上箭头 |  `\uparrow`   |
|  $\Uparrow$   | 上箭头 |  `\Uparrow`   |
| $\downarrow$  | 下箭头 | `\downarrow`  |
| $\Downarrow$  | 下箭头 | `\Downarrow`  |
| $\leftarrow$  | 左箭头 | `\leftarrow`  |
| $\Leftarrow$  | 左箭头 | `\Leftarrow`  |
| $\rightarrow$ | 右箭头 | `\rightarrow` |
| $\Rightarrow$ | 右箭头 | `\Rightarrow` |

[更多箭头字符](https://zh.wikipedia.org/wiki/Help:%E6%95%B0%E5%AD%A6%E5%85%AC%E5%BC%8F#%E7%AE%AD%E5%A4%B4)

<div STYLE="page-break-after: always;"></div>

## 省略号



|   符号   |       含义       | Markdown |
| :------: | :--------------: | :------: |
| $\ldots$ | 底端对齐的省略号 | `\ldots` |
| $\cdots$ | 中线对齐的省略号 | `\cdots` |
| $\vdots$ | 竖直对齐的省略号 | `\vdots` |
| $\ddots$ |  斜对齐的省略号  | `\ddots` |

<div STYLE="page-break-after: always;"></div>

## 希腊字母



|    大写    |  Markdown  |     小写      |   Markdown    |
| :--------: | :--------: | :-----------: | :-----------: |
|    $A$     |    `A`     |   $\alpha$    |   `\alpha`    |
|    $B$     |    `B`     |    $\beta$    |    `\beta`    |
|  $\Gamma$  |  `\Gamma`  |   $\gamma$    |   `\gamma`    |
|  $\Delta$  |  `\Delta`  |   $\delta$    |   `\delta`    |
|    $E$     |    `E`     |  $\epsilon$   |  `\epsilon`   |
|            |            | $\varepsilon$ | `\varepsilon` |
|    $Z$     |    `Z`     |    $\zeta$    |    `\zeta`    |
|    $H$     |    `H`     |    $\eta$     |    `\eta`     |
|  $\Theta$  |  `\Theta`  |   $\theta$    |   `\theta`    |
|    $I$     |    `I`     |    $\iota$    |    `\iota`    |
|    $K$     |    `K`     |   $\kappa$    |   `\kappa`    |
| $\Lambda$  | `\Lambda`  |   $\lambda$   |   `\lambda`   |
|    $M$     |    `M`     |     $\mu$     |     `\mu`     |
|    $N$     |    `N`     |     $\nu$     |     `\nu`     |
|   $\Xi$    |   `\Xi`    |     $\xi$     |     `\xi`     |
|    $O$     |    `O`     |  $\omicron$   |  `\omicron`   |
|   $\Pi$    |   `\Pi`    |     $\pi$     |     `\pi`     |
|    $P$     |    `P`     |    $\rho$     |    `\rho`     |
|  $\Sigma$  |  `\Sigma`  |   $\sigma$    |   `\sigma`    |
|    $T$     |    `T`     |    $\tau$     |    `\tau`     |
| $\Upsilon$ | `\Upsilon` |  $\upsilon$   |  `\upsilon`   |
|   $\Phi$   |   `\Phi`   |    $\phi$     |    `\phi`     |
|            |            |   $\varphi$   |   `\varphi`   |
|    $X$     |    `X`     |    $\chi$     |    `\chi`     |
|   $\Psi$   |   `\Psi`   |    $\psi$     |    `\psi`     |
|  $\Omega$  |  `\Omega`  |   $\omega$    |   `\omega`    |

<div STYLE="page-break-after: always;"></div>

## 其他常用符号



|     符号      |     含义     |   Markdown    |
| :-----------: | :----------: | :-----------: |
|   $\infty$    |     无穷     |   `\infty`    |
|   $\imath$    |     虚数     |   `\imath`    |
|   $\jmath$    |     虚数     |   `\jmath`    |
| $\vec {矢量}$ |   矢量符号   | `\vec {矢量}` |
|   $\dot{a}$   |   一阶导数   |   `\dot{a}`   |
|  $\ddot{a}$   |   二阶导数   |  `\ddot{a}`   |
|   $\exists$   |     存在     |   `\exists`   |
|   $\nabla$    | 向量微分算子 |   `\nabla`    |
|      $⊥$      |  垂直/正交   |      `⊥`      |
|   $\angle$    |   角/斜度    |   `\angle`    |
|   $\forall$   | 全称量化符号 |   `\forall`   |
|  $\because$   |     因为     |  `\because`   |
| $\therefore$  |     所以     | `\therefore`  |



[更多LaTex字符参考维基百科]()

