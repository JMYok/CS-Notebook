# 虚拟化技术
将一台物理机虚拟为多台逻辑主机。
![](https://pic.imgdb.cn/item/66961854d9c307b7e9a5565a.png)

# Docker和虚拟机的区别
![](https://pic.imgdb.cn/item/669617e2d9c307b7e9a4be01.png)
- 虚拟机需要一整个操作系统，包括内核、各种工具，甚至图形界面，还有其他的内存、CPU、内存、网络等资源，但是我们只是需要在这个环境下启动一个Nginx服务器对外提供服务即可，所以造成了资源的浪费；而docker通过利用宿主主机的OS，以及Namespace实现进程隔离，cgroups实现资源约束，来创建多个容器，所以能够创建成百上千个容器。
    > 从图中可看出，**容器是一种虚拟化技术，docker只是容器的其中一种实现方式，是容器化的解决方案和平台**。
- 虚拟机启动时间为分钟级，而docker为秒级。

# 原理
## clone系统调用
clone() 是 fork() 的一个更加通用和灵活的版本。
可以通过传递不同的标志（flags）来指定共享哪些资源（如内存空间、文件描述符、信号处理、工作目录等）。
## Namespace
向通过clone传递系统调用参数，**实现进程之间资源的隔离**。
![](https://pic.imgdb.cn/item/669b5fd7d9c307b7e9261251.png)
```c
#define _GNU_SOURCE
#include <sys/types.h>
#include <sys/wait.h>
#include <stdio.h>
#include <sched.h>
#include <signal.h>
#include <unistd.h>

/* 定义一个给 clone 用的栈，栈大小1M */
#define STACK_SIZE (1024 * 1024)
static char container_stack[STACK_SIZE];

char* const container_args[] = {
    "/bin/bash",
    NULL
};

int container_main(void* arg)
{
    /* 查看子进程的PID，我们可以看到其输出子进程的 pid 为 1 */
    printf("Container [%5d] - inside the container!\n", getpid());
    sethostname("container",10); /* 设置hostname */
    /* 重新mount proc文件系统到 /proc下 */
    system("mount -t proc proc /proc");
    /* 直接执行一个shell，以便我们观察这个进程空间里的资源是否被隔离了 */
    execv(container_args[0], container_args); 
    printf("Something's wrong!\n");
    return 1;
}

int main()
{
    printf("Parent [%5d] - start a container!\n", getpid());
    /* 调用clone函数，其中传出一个函数，还有一个栈空间的（为什么传尾指针，因为栈是反着的） */
    int container_pid = clone(container_main, container_stack+STACK_SIZE,CLONE_NEWNS |CLONE_NEWPID|CLONE_NEWIPC | CLONE_NEWUTS|SIGCHLD, NULL);
    /* 等待子进程结束 */
    waitpid(container_pid, NULL, 0);
    printf("Parent - container stopped!\n");
    return 0;
}
```
- **UTS Namespace**：启用CLONE_NEWUTS 隔离主机名和域名
- **IPC Namespace**：启用CLONE_NEWIPC 隔离IPC防止进程间通信
- **PID Namespace**：启用CLONE_NEWPID 创建子进程在容器中，pid=1
- **Mount Namespace**：Mount Namespace 是 Linux 内核实现的第一个 Namespace，从内核的 2.4.19 版本开始加入。启用CLONE_NEWNS 在子进程中重新mount了/proc文件系统，ps，top只能看到容器内的进程。通过CLONE_NEWNS创建mount namespace后，父进程会把自己的文件结构复制给子进程中。而子进程中新的namespace中的所有mount操作都只影响自身的文件系统，而不对外界产生任何影响，这样可以做到比较严格地隔离。
- **User Namespace**：User Namespace 主要是用来隔离用户和用户组的。一个比较典型的应用场景就是在主机上以非 root 用户运行的进程可以在一个单独的 User Namespace 中映射成 root 用户。使用 User Namespace 可以实现进程在容器内拥有 root 权限，而在主机上却只是普通用户。
- **Net Namespace**： Net Namespace 是用来隔离网络设备、IP 地址和端口等信息的。Net Namespace 是用来隔离网络设备、IP 地址和端口等信息的。Net Namespace 可以让每个进程拥有自己独立的 IP 地址，端口和网卡信息。例如主机 IP 地址为 172.16.4.1 ，容器内可以设置独立的 IP 地址为 192.168.1.1。
> 理解：通过clone创建子进程，并执行execv执行bash程序，进入新的地址空间，之后再通过隔离参数，使得容器进程和外界进程隔离。

## Cgroups
- cgroups（全称：control groups）是 Linux 内核的一个功能，它可以实现限制进程或者进程组的资源（如 CPU、内存、磁盘 IO 等）。
- 主要功能：
  - 资源限制： 限制资源的使用量，例如我们可以通过限制某个业务的内存上限，从而保护主机其他业务的安全运行。
  - 优先级控制：不同的组可以有不同的资源（ CPU 、磁盘 IO 等）使用优先级。
  - 审计：计算控制组的资源使用情况。
  - 控制：控制进程的挂起或恢复。
- 通过子系统和控制组实现，常用的cgroups子系统都挂载到linux上了。比如要限制cpu，创建文件夹/sys/fs/cgroup/cpu/mydocker就好，其中会自动创建很多问价，在taks文件中添加进程id，以及在其他文件中调整配置即可。Docker也是这样操作的。
![](https://pic.imgdb.cn/item/66a9d016d9c307b7e9eaa56a.png)
  
## UnionFS
- 联合文件系统能够把多个目录内容联合挂载到同一目录下，从而形成一个单一的文件系统。其是docker分层镜像的基础，**使得镜像的每一层能够被共享，从而节省空间。**
- UnionFS的实现最开始是AUFS，还有DeviceMapper和OverlayFS，现在默认是OverlayFS。

# 参考
- https://www.bilibili.com/video/BV14s4y1i7Vf
- [DOCKER基础技术：LINUX NAMESPACE（上）](https://coolshell.cn/articles/17010.html)
- [为什么需要docker？](https://yeasy.gitbook.io/docker_practice/introduction/why)
- [09 资源隔离：为什么构建容器需要 Namespace ？](https://learn.lianglianglee.com/%E4%B8%93%E6%A0%8F/%E7%94%B1%E6%B5%85%E5%85%A5%E6%B7%B1%E5%90%83%E9%80%8F%20Docker-%E5%AE%8C/09%20%20%E8%B5%84%E6%BA%90%E9%9A%94%E7%A6%BB%EF%BC%9A%E4%B8%BA%E4%BB%80%E4%B9%88%E6%9E%84%E5%BB%BA%E5%AE%B9%E5%99%A8%E9%9C%80%E8%A6%81%20Namespace%20%EF%BC%9F.md)
- [10 资源限制：如何通过 Cgroups 机制实现资源限制？](https://learn.lianglianglee.com/%e4%b8%93%e6%a0%8f/%e7%94%b1%e6%b5%85%e5%85%a5%e6%b7%b1%e5%90%83%e9%80%8f%20Docker-%e5%ae%8c/10%20%20%e8%b5%84%e6%ba%90%e9%99%90%e5%88%b6%ef%bc%9a%e5%a6%82%e4%bd%95%e9%80%9a%e8%bf%87%20Cgroups%20%e6%9c%ba%e5%88%b6%e5%ae%9e%e7%8e%b0%e8%b5%84%e6%ba%90%e9%99%90%e5%88%b6%ef%bc%9f.md)
- [14 文件存储驱动：AUFS 文件系统原理及生产环境的最佳配置](https://learn.lianglianglee.com/%e4%b8%93%e6%a0%8f/%e7%94%b1%e6%b5%85%e5%85%a5%e6%b7%b1%e5%90%83%e9%80%8f%20Docker-%e5%ae%8c/14%20%20%e6%96%87%e4%bb%b6%e5%ad%98%e5%82%a8%e9%a9%b1%e5%8a%a8%ef%bc%9aAUFS%20%e6%96%87%e4%bb%b6%e7%b3%bb%e7%bb%9f%e5%8e%9f%e7%90%86%e5%8f%8a%e7%94%9f%e4%ba%a7%e7%8e%af%e5%a2%83%e7%9a%84%e6%9c%80%e4%bd%b3%e9%85%8d%e7%bd%ae.md)
  