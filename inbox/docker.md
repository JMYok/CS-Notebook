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
## namespace
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
- 启用CLONE_NEWUTS 隔离主机名和域名
- 启用CLONE_NEWIPC 隔离IPC防止进程间通信
- 启用CLONE_NEWPID 创建子进程在容器中，pid=1
- 启用CLONE_NEWNS 在子进程中重新mount了/proc文件系统，ps，top只能看到容器内的进程。通过CLONE_NEWNS创建mount namespace后，父进程会把自己的文件结构复制给子进程中。而**子进程中新的namespace中的所有mount操作都只影响自身的文件系统**，而不对外界产生任何影响，这样可以做到比较严格地隔离。
> 理解：通过clone创建子进程，并执行execv执行bash程序，进入新的地址空间，之后再通过隔离参数，使得容器进程和外界进程隔离。


# 参考
- https://www.bilibili.com/video/BV14s4y1i7Vf
  