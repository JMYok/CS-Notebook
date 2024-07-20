# C 语言
在 Linux 下，一般使用 gcc 编译 C 语言代码。 gcc 可以通过包管理工具进行安装，以 Ubuntu 为例：
```shell
$ sudo apt install gcc
```
centOS：
```shell
$ yum install gcc
```

接下来，我们编译一个非常简单的 C 语言程序 hello_world.c .代码如下：
```c
//hello_world.c
#include <stdio.h>

int main() {
    printf("Hello, world!\n");
    return 0;
}
```
你可以使用任何编辑工具来编写代码vim，甚至记事本均可。

代码编辑完毕后，运行 gcc 命令进行编译：
```shell
$ ls
hello_world.c
$ gcc -o hello_world hello_world.c
```
其中， -o 选项指定可执行程序名， hello_world.c 是源码文件。 不出意外，当前目录下将出现一个可执行文件：
```shell
$ ls
hello_world  hello_world.c
```
最后，还是在命令行下，将程序运行起来>
```shell
$ ./hello_world
Hello, world!
```
C++ 语言编译与 C 语言类似，只不过编译工具不再是 gcc ，而是 g++ 。 同样地， g++ 也可以通过包管理工具来安装：

Ubuntu:
```shell
$ sudo apt install g++
```
centOS：
```shell
$ yum install g++
```
# 多文件
大型程序一般由多个文件组成，编译多文件程序，将所有源码文件传给编译器即可。

以 C 语言为例，将 hello_world.c 拆解成两个文件进行演示。 首先是 say.c ：
```c
//multi-file/say.c
#include <stdio.h>

void say_hello() {
    printf("Hello, world!\n");
}
```
say.c 定义了一个函数，名为 say_hello 用于输出 Hello, world! 。

在 hello_world.c 中，直接调用 say_hello 即可：
```c
//multi-file/hello_world.c

void say_hello();

int main() {
    say_hello();
    return 0;
}
```
编译这个由两个文件组成的程序，步骤也非常简单：
```shell
$ gcc -o hello_world hello_world.c say.c
```
请注意，**需要在 hello_world.c 中申明函数 say_hello 的原型(第 1 行)**，否则编译将输出以下警告：
```shell
hello_world.c:17:5: warning: implicit declaration of function 'say_hello' is invalid in C99 [-Wimplicit-function-declaration]
        say_hello();
        ^
1 warning generated.
```
原型申明相当于告诉编译器，say_hello 函数在别处定义，调用时不需要参数，也没有返回值。 **编译器编译 hello_world.c 时需要这个信息，因为 say_hello 函数的定义不在该文件下**。
# 编译流程
如下图，程序编译可以进一步分成 编译 ( Compile )和 链接 ( Link )两个阶段：
![](https://pic.imgdb.cn/item/669b667fd9c307b7e92fe1c0.png)
接下来，我们分阶段编译 ，源文件如下：
```shell
$ cd multi-file2
$ ls
hello_world.c  say.c  say.h
```
先编译 say.c 文件，生成say.o对象文件：
```shell
$ gcc -c say.c
$ ls
hello_world.c  say.c  say.h  say.o
```
再编译 hello_world.c文件，生成 hello_world.o 对象文件：
``` shell
$ gcc -c hello_world.c
$ ls
hello_world.c  hello_world.o  say.c  say.h  say.o
```
最后 链接 俩对象文件，生成可执行程序：
```shell
$ gcc -o hello_world hello_world.o say.o
$ $ ./hello_world
Hello, world!
```
>为啥要分阶段编译？
>
> 分阶段编译最大的好处是，可以进行部分编译—— 只编译有变更部分 。假设例子中， hello_world.c 有变更，而 say.c 没有变更。 那么，我们只需要编译 hello_world.c 生成新的 hello_world.o 对象文件， 最后再跟先前的 say.o 文件链接生成新的可执行文件即可
> 换句话将，我们**省去了编译 say.c 的麻烦！ 这个小程序可能不明显，在大型程序中节省的编译时间非常可观。**
# 自动构建
我们尝到部分编译的好处，但是区分哪些源码文件有变更是个麻烦事儿。 而且，手工敲这么多编译命令也很无聊，我们急需一种能够自动构建的方法。
这时，我们可以借助自动化构建工具[make](https://en.wikipedia.org/wiki/Make_(software)) ：

Ubuntu:
```shell
$ sudo apt install make
```
centOs:
```shell
$ yum install make
```
构建规则定义在 [Makefile](https://github.com/fasionchan/learn-linux/blob/master/src/development-environment/c-cpp/auto-build/Makefile) 里：
```shell
.DEFAULT_GOAL := run

say.o: say.c
	gcc -o say.o -c say.c

hello_world.o: hello_world.c
	gcc -o hello_world.o -c hello_world.c

hello_world: say.o hello_world.o
	gcc -o hello_world say.o hello_world.o

run: hello_world
	./hello_world

clean:
	rm -f *.o
	rm -f hello_world
```
Makefile 规则可以很复杂，这里只介绍最基本的。

Makefile 大致可以理解成**目标 、 依赖**以及**构建指令**。

以第 3-4 行为例， say.o 是构建**目标**，say.c 是**依赖** ，其后以制表符( \t )缩进的行是**构建指令**。换句话将，要构建say.o需要依赖say.c源码文件，构建方法是执行gcc编译命令。

依赖定义清楚之后， make可以决定什么情况下需要重新构建，什么情况下则不用。 如果 say.c 有变更( mtime 比 say.o 大)，则需要重新运行构建指令生成新的 say.o 。 反之则不用。

此外，目标可以被其他目标所依赖，就像搭积木一样！ 执行程序( run )，依赖二进制程序( hello_world )， 二进制程序则依赖两个目标文件( say.o, hello_world.o )，而目标文件又分别依赖各自的源码文件。 通过递归， make 自动找到了构建并运行程序的方法！

Makefile 定义好后，运行 make 命令加上目标名即可构建指定的目标。 例如，编译 say.o ：
```shell
$ make say.o
```
或者编译并生产可执行程序( hello_world )：
```shell
$ make hello_world
```
make 命令将确保俩子目标( say.o 以及 hello_world.o )先被正确构建。

最后，一起感受一下自动构建有多爽。进入 auto-build 目录：
```shell
$ cd auto-build
$  ls
hello_world.c  Makefile  say.c  say.h
```
执行 make 命令：
```shell
$ make
gcc -o say.o -c say.c
gcc -o hello_world.o -c hello_world.c
gcc -o hello_world say.o hello_world.o
./hello_world
Hello, world!
```
一个 make 命令搞定从编译到执行的所有环节！
> 缺省情况下， Makefile 定义的第一个目标是默认目标。我们在 Makefile 第一行显式定义了默认目标。

由于没有变更，再次构建时自动省略编译环节：
```shell
$ make
./hello_world
Hello, world!
```
顺便提一下，我们的 Makefile 还定义了一个用于清理编译结果的目标—— clean ：
```shell
$ ls
Makefile  hello_world  hello_world.c  hello_world.o  say.c  say.h  say.o
$ make clean
rm -f *.o
rm -f hello_world
$ ls
Makefile  hello_world.c  say.c  say.h
```
清理编译结果在打包源码、进行全新编译等场景特别有用。
# 程序库
## 静态库

## 动态库

# 参考
- [C/C++ 开发环境(终端)](https://linux.fasionchan.com/zh_CN/latest/development-environment/c-cpp.html)