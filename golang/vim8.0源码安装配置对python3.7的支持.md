---
title: vim8.0源码安装配置对python3.7的支持

date: 2018-01-014

tags: [vim安装, linux学习]

---

##### 摘要
> 在一台新的服务器上搭建自己的开发环境，在vundle安装插件时提示vim版本过低，所以需要升级下vim。而YouCompleteMe插件需要安装python。在安装的过程中python一直不正确，特做此记录。


##### vim安装

先下载最新版的vim，解压后进入到vim/src文件夹。
因为装有旧版的vim所以需要先卸载vim。
卸载命令:`make uninstall` 
清楚配置:`make clean` 或者执行： `make distclean`

然后是最重要的一步，我在这里因为开始没有设置python的支持，所以又重新编译一次。

配置vim源码的编译属性
```
./configure --with-features=huge \
            --enable-multibyte \
            --enable-rubyinterp=yes \
            --enable-python3interp=yes \
            --with-python3-config-dir=/usr/local/python37/lib/python3.7/config-3.7m-x86_64-linux-gnu \
            --enable-perlinterp=yes \
            --enable-luainterp=yes \
            --enable-cscope \
            --prefix=/usr/local/vim8
```

这里需要注意三个参数  
`--enable-python3interp` 这里需要设置为`yes`默认值是`no`
`--enable-python3-config-dir`这里需要设置为你自己的python的config路径 我的是`/usr/local/python37/lib/python3.7/config-3.7m-x86_64-linux-gnu`
`--prefix` 设置vim的安装目录，如果不设置会和系统应用安装在一起，最好设置一下。

执行完配置文件之后 `make && make install`(可以分开执行`make` 和 `make install`)。

原本以为到这里就可以大功告成了，没想到执行 `vim --version`后发现python3仍然未被支持，make过程也没有任何报错。
找了很久也没有发现问题的所在，不断尝试发现在执行python后显示当前的版本还是python2.6才知道是python的问题，然后修改了python的环境变量重新清除配置，才得以安装成功。 然后为了验证删掉环境变量又重新装了一次，没成功，所以确定是这里的问题。   
环境变量配置可根据需要自行搜索。
我这里是因为设置在自己的用户下所以改的是  /home/name/.bash_profile 文件 
````
/usr/local/python37/bin:%PATH:$HOME/bin
export PATH
```
然后执行 `..bash_profile`让环境变量生效。