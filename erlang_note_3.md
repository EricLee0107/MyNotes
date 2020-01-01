# Erlang/OTP杂记３

### `-name`和`-sname`的区别
`-name`给节点设置名字，形式一般是Name@Host，Host在网段内是完全限定名，如果是跨网段，则需要使用公网IP; `-sname`是使用短名字,形式一般是Name 没有Host。
`-name`适用于配有DNS的普通网络环境，`-sname`适用于完全限定域名不可用的情况比如同一局域网内两台机器可以通过无线网互联但通过DNS无法链接的情况。
采用长节点名启动的节点无法和使用短节点名启动的节点之间无法互联，不可形成集群。

### erlang套接字的三种模式
[erlang套接字](https://www.cnblogs.com/yanwei-wang/p/4710436.html)

### RPC(远程过程调用)

