# Linux下建立NFS Server服务
这里只说`Linux`

有几大要素
1. 服务依赖软件得安装上，这个一般都已经自带了   
不管有没有都可以安装一下
> ```Bash
> #Fedora
> sudo dnf install nfs-server
> #Ubuntu
> sudo apt-get install nfs-kernel-server
> ```

2. 防火墙把使用到的端口打开
一般会用到`111` `2049`, 还有一个从`/etc/service`里面查`mountd`的端口(打开文本`/etc/services`，查找`mountd`看后面的端口号是什么)
比如要打开防火墙的`111/tcp`端口   
> `Fedora`
> ```Bash
> firewall-cmd --permanent --add-port=111/tcp
> ```

3. `/etc/exports`文件里给予要访问的端侧IP以权限
打开文本`/etc/exports`，没有的话创建一个，添加配置，表明你要允许那些IP地址连接这台`PC`的哪些目录   
比如允许192.168.4.0/24网段的所有设备访问`/home/liwcv/hi_nfs`
> ```
> /home/liwcv/hi_nfs 192.168.4.0/24(rw,no_root_squash)
> ```
关于`/etc/exports`的详细描述可上网搜索，有很多。

---

上面三步完成之后就可以开启服务了   
`Fedora`
```Bash
systemctl restart nfs
```

`Ubuntu`
```Bash
systemctl restart nfs-server
```

如果不是开机自动启动的话可以运行下面的命令
```Bash
systemctl enable nfs-server.service
```

---
## 测试
可以用开发板或者另一台电脑进行连接测试
比如开发板的`IP`地址为`192.168.4.34`，`NFS server`主机的`IP`地址为`192.168.4.100`，可以用下面命令把主机的`/home/liwcv/hi_nfs`目录挂载到板端的`/home`
```Bash
mount -t nfs -o nolock 192.168.x.x:/home/liwcv/hi_nfs /home
```
没有错误报出即为成功。  
为了以后方便使用，可以把这条命令写成脚本文件放在板子上，这样就不用每次输入那么长。