# Erlang/OTP杂记１

i() 查看当前系统的运行时信息
memory() 查看内存信息
shell 中v(N) 获取某一行的运行结果；N为erl shell中的行号
shell 中Ctrl-G之后各按键的意义：

1. 整数无大小限制
2. 浮点数为双精度，必须以数字开头，如0.1　０不可省略
3. ++ 为从前向后加入
4. erlang 不同数据类型之间是可以进行比较的，数值<原子<元祖<列表
5. =:= 与== 的区别：前者要求数据类型和值都相等，后者只要求值相等
6. 所有外部传入的数据理论上都是需要检查数据合法性的。
7. 量大测试工具：EUnit、Common Test;
8. erlang 应用分为主动应用和库应用，主动应用存在生命周期，需要先启动；库应用则不涉及启动及停止
9. erlang子进程规范：{ID,Start,Restart,Shotdown,Type,Modules},Modules用于热更
10. gen_server回调call的返回值中不设置超时则被重置为inifinity。
11. erlang 代码规则是“要事优先”，重要的代码放在文件的前面。
12. ets:insert/2会覆盖旧的数据
13. epmd:erlang节点(分布式)在启动时如果本地机器还没有启动epmd服务则会随着第一个节点的启动在本地启动epmd服务。两个节点想通信本地计算机的epmd会联系远程计算机的epmd说想链接xx节点，如果有则回复一个端口号（默认是4369)　epmd间不主动搜寻其他Epmd
14. 同cookie的节点间可以相互通信，若节点设置了不同的cookie则不会因net_adm:ping()而互联。
15. 远程指定节点上的指定进程发送消息:`{进程名，节点} ! 消息` 或者　`Pid ! 消息`。注:Pid中包含节点信息。
16. 本地发送消息:`进程名!消息`　或者　`pid ! 消息`
17. <5135.73.0> 进程标识中第一位非0代表是远程节点的进程。
18. 进程崩溃字后信箱内容会丢失但变量关系保留。
19. 从位串中提取内容可以使用`<=`
20. `!`读作`bang`
21. ets允许多个行具有相同键但不可以完全相同
22. ets:lookup/2返回值是列表
23. 尾递归可以减少后续待处理问题加入栈内存导致栈内存耗尽。
24. 尾递归执行之后会删除栈内当前调用信息
25. 调用相同函数的尾递归可以重用栈顶调用信息
26. 尾递归空间消耗恒定



### Mnesia
|函数|功能|说明|
|:--|:--|:--|
|start/0|启动|
|stop/0 |停止|
|info/0 |查询|
|create_table/2|创建表|第一个为主见，可以通过`{type,类型}`设置表类型，通过`{index,[索引]}`设置索引|
|transaction/1|事务|慢但安全|
|write/1|写入|慢但数据安全|
|read/1|读取|慢但数据安全|
|dirty_write/1|写入|快但为脏操作，并不保证数据安全|
|dirty_read/1| 读取| 快但为脏操作，并不保证数据安全
|dirty_...||一些脏操作，但相对速度较快|
|select/2|匹配查询|`_`{限定在head部分使用}——无所谓，任意值都可以;`$_`(仅限于在Results和Conditions中使用)——与查询条件匹配的整条记录;`$$`(仅限于在Results和Conditions中使用)——等价于一次罗列出在Head部分酦醅的所有变量`$1`、`$2`等。|
|dirty_index_read/3|索引查询|
|delete_schema/1|
|add_table_copy/3|
|system_info/1|

表类型:`set`、`ordered_set`、`bag`。
表存储类型：`ram_copies`、`disc_copies`、`disc_only_copies`
ram_copies:代表存入内存
disc_copies:磁盘和内存，`shema`表就是这个类型的
disc_only_copies:磁盘，不支持表类型为ordered_set的表









### 待补充概念/知识
1. 正则表达式
2. 网络的多播与广播？
3. 如何添加依赖　P205
4. net_adm:ping/1
6. OTP文档heart模块学习
7. 套接字的主动模式与被动模式
8. application:start　启动类型
9. RPC(远程过程调用)
10. EDoc的使用
11. ets表常用函数和使用中的问题
12. ets表Options的使用
13. gen_server:cal/3返回nodedown是很么原因？还有那些返回情况?
14. process_flag(trap_exit,true)什么用法？有什么特殊使用场景?
15. 不同节点之间如何通过pid查找到对方并成功传递消息。
16. rpc:call和gen_server:call的比较;rpc和gen_server的关系
17. erlang:spawn_monitor
