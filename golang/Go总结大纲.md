# Go总结大纲

## Go基础

- [ ] go struct 能不能比较？

- [ ] go defer (for defer)

- [ ] select 可以用于什么

- [ ] slice,len,cap,共享，扩容

- [ ] map如何实现顺序读取

- [ ] go的反射：go的反射包如何找到对应的方法

- [ ] go使用踩过什么坑（for range，数据库连接defer close）

- [ ] go优缺点

- [ ] go命令，go get，go tool，go test，go vet

- [ ] go的值传递和引用

- [ ] go的new和make区别

- [ ] go的channel：

  - [ ] 退出程序时怎么防止channel没有消费完

  

## Go实操

- [ ] 实现set
- [ ] 实现消息队列（多生产者，多消费者），手写代码？不用channel如何实现
- [ ] 大文件排序
- [ ] client如何实现长链接
- [ ] 手写循环队列

## Go进阶

- [ ] go的调度
- [ ] go的gc
- [ ] goroutine调度用了什么系统调用
- [ ] goroutine泄漏有没有处理，设置timeout，select加定时器
- [ ] go怎么从源码编译到二进制文件
- [ ] go的锁如何实现，用了什么cpu指令
- [ ] go的runtime如何实现
- [ ] go什么情况下会发生内存泄漏？（他说ctx没有cancel的时候，这个真不知道）
- [ ] channel的实现？
- [ ] map扩容和迁移

## Go包

- [ ] context包
- [ ] net包
- [ ] io包
- [ ] sync.Pool: 为什么使用？里面的对象是固定的吗？

用途









## Go高级

- [ ] go tool cgo详解

## Go源码层

