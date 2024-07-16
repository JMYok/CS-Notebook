# 虚拟化技术
将一台物理机虚拟为多台逻辑主机。
![](https://pic.imgdb.cn/item/66961854d9c307b7e9a5565a.png)

# Docker和虚拟机的区别
![](https://pic.imgdb.cn/item/669617e2d9c307b7e9a4be01.png)
- 虚拟机需要一整个操作系统，包括内核、各种工具，甚至图形界面，还有其他的内存、CPU、内存、网络等资源，但是我们只是需要在这个环境下启动一个Nginx服务器对外提供服务即可，所以造成了资源的浪费；而docker通过利用宿主主机的OS，以及Namespace实现进程隔离，cgroups实现资源约束，来创建多个容器，所以能够创建成百上千个容器。
    > 从图中可看出，**容器是一种虚拟化技术，docker只是容器的其中一种实现方式，是容器化的解决方案和平台**。
- 虚拟机启动时间为分钟级，而docker为秒级。

# 参考
- https://www.bilibili.com/video/BV14s4y1i7Vf
  