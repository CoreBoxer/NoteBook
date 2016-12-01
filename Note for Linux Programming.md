        Linux系统编程

POSIX:可移植性操作系统接口
目录是可读名称到索引编号的之间的映射，名称和索引节点之间的配对称为链接（Link）。
目录也是普通文件的形式存在，但只能通过特殊系统调用来操作
目录间内的链接可以指向其他目录的索引节点，引起目录可以嵌套到其他目录中，形成目录层
内核打开路径名时，会遍历路径中每个目录项（dentry），找到指向下一个索引节点的链接，依次向下

多个不同名称的链接映射到同一个索引节点时，称链接为硬链接
当硬链接数减为0时，文件及其对应索引节点才会从文件系统删除
硬连接不能跨越多个文件系统，需要 符号链接
符号链接可以指向任何地方甚至不存在的文件，它存有 目标绝对路径

Linux四种特殊文件：    块设备文件 字符设备文件    命名管道    UNIX域套接字
通过系统调用创建他们

字符设备：作为线性字节队列访问，驱动程序把字符写入队列，用户空间读出队列
块设备：作为字节数组访问，驱动把字节映射到客轮值的设备上，用户空间可按任意顺序和位置读 
命名管道：FIFO，将一个程序输出以“管道”通向另一程序输入，它们通过访问同一文件实现
套接字：支持不同进程间通信，可本机，可网络

扇区（sector）：块设备最小寻址单元，介质物理属性
块（block）：文件系统最小寻址单元，文件系统的抽象，不是物理介质的抽象
    块大小一般是2的指数倍的扇区大小（512B、1KB、4KB）
页：内存最小寻址单元

    进程

ELF（Executable and Linkable Format）

代码段：线性目标代码块
数据段：初始化的数据
bss段：未初始化的全局数据


mmu（内存管理单元）：
    * 提供虚拟内存功能，使系统可执行比内存更大的程序
    * 保护代码段等需要保护的内存区

IPC(Inter-Process Communication)进程间通信

gcc参数  
    * -o 产生目标
    * -E 预处理    头文件展开宏展开，.i文件
    * -S 编译     产生汇编文件，.s
    * -c 取消链接步骤，直接生成目标文件 .o
    * -Wall 对源文件代码有问题的部分发出警告
    * -Idir 指定头文件目录路径
    * -Ldir 指定库文件目录路径
    * -llib 链接lib库
    * -g 目标文件中添加调试信息，以便gdb调试
    
一个与共享库链接的可执行文件仅仅包含它用到的函数入口地址的一个表，而不是外部函数所在目标文件的整个机器码

    生成静态库()
hello_fn.h
hello_fn.c
main.c
gcc -Wall hello_fn.c -o hello_fn.o
ar rcs libhello.a hello_fn.o 
ar是gnu归档工具，rcs表示(replace and create)
gcc -Wall main.c libhello.a -o main
gcc -Wall -L. main.c -o main -lhello


    生成共享库(.so）
shared: 表示生成共享库格式
fPIC：产生位置无关码(position independent code)
库名规则：libxxx.so
示例：gcc -shared -fPIC hello.o –o libhello.so
使用共享库
编译选项
l：链接共享库，只要库名即可(去掉lib以及版本号)
L：链接库所在的路径.
示例：
gcc main.o -o main –L. -lhello

    程序运行时的动态链接库路径配置
1、拷贝.so文件到系统共享库路径下
    一般指/usr/lib
2、更改LD_LIBRARY_PATH
3、配置ld.so.conf，ldconfig更新ld.so.cache
    然后调用ldconfig更新

静态库和共享库同时存在的时候，优先使用共享库

查看程序调用了那些库
    ldd main 即可查看调用了哪些库

        Makefile
显式声明伪目标可以防止与同名文件冲突
    .PHONY:clean

变量：
    定义 OBJECTS=main.c print.c
    使用 $(OBJECTS)

自动化符号：
    $@  所有目标
    $<  第一个依赖
    $^  所有依赖

@ 取消命令执行输出

%.c 当前列表的所有.c文件

.c.o:   当前所有C文件变成O文件

        GDB（GNU Debugger）调试
gdb target  启动GDB
l（list）    显示C程序，Enter显示下一页
l（list） 文件名：n 查看第n行
l（list） 文件名：函数名 查看函数
b（break） n 在第n行添加断点
b（break）函数名 在程序人口地址插入断点
i（info） break 查看所有断点信息
r（run） 执行程序（或重新执行）
s（step）单步（进入函数）
n（next）不进入函数
u（until） 跳出循环
c          跳到下一个断点
p（print） 变量名   显示变量内容
f（finish） 退出当前函数并打印函数返回时的堆栈地址和返回值及参数值等信息
q（quit）  退出gdb
break if <condition> - 条件成立时程序停住。
    如break if i=20  当i等于20时停住
info break(i b) - 查看断点
watch expr - 一旦变量值发生改变，程序停住
delete n - 删除断点

print - 查看变量值
ptype - 查看类型
print 数组名 - 查看数组
    此时数组名代表整个数组，&数组才是基地址
print *array@len - 查看动态内存
print x=5 - 改变运行时数据


回车（Enter） 重复上一个命令

        系统调用

每个系统调用被赋予一个系统调用号
在i386平台上，通过int 0x80 软中断进入内核空间
eax存放系统调用号
ebx、ecx、edx、esi、edi存储系统调用的参数
    超过5个参数的系统调用，用一个寄存器指向用户空间存储参数的缓存区


函数：perror("dada");
输出内容：dada：错误码对应的字符串

函数：str = strerror(错误码)
    将 错误码 转换为对应的 字符串
注意：包含 string.h

函数:fprintf("stderr",
        "close error with msg: %s\n",
        strerror(errno))

文件指针    文件描述符
stdin       STDIN_NO
stdout      STDOUT_NO
stderr      STDERR_NO

fileno(文件指针):
    文件指针转换为文件描述符
fileopen(文件描述符):
    文件描述符转换为文件指针

文件权限和umask：
    new prio = prio &~ umask
    不是算数减法，而是逻辑减法
    0777位1+2+4会被0022筛掉二进制第2位和第5位
    0755为1+4没有二进制第2位和第5位，所以仍为0755

当整个程序结束时会把所有打开的文件描述符关闭

目录访问函数：
opendir     readdir     closedir
rmdir       mkdir
chmod       fchmod
chown       fchown

获取文件元数据
int stat(const char *path, struct stat *buf);
int fstat(int fd, struct stat *buf);
int lstat(const char *path, struct stat *buf);\\只可获取符号链接文件本身的元数据

readlink：获取链接文件指向的地址

同一进程多次打开的同一文件的文件表是各自独立的，但v节点表使用同一个

v节点信息？
i节点信息？

函数
    void *memset(void *s, int c, size_t n);
        在s指向的内存区的起始区域内填写n个c

复制文件描述符
    dup(int oldfd);
    dup2(int oldfd, int newfd);等价于close newfd然后dup(oldfd);
    fcntl(fd, F_DUPFD, fd_start);从fd_start开始搜索可用的空文件描述符，而dup从0开始

输出重定向
    如先把1号文件描述符（标准输出）关闭，然后复制其它文件描述符，该文件描述符就会和1指向统一文件，标准输出变成该文件

fcntl多功能函数
    * 复制文件描述符
        - F_DUPFD(long)
    * 复制文件描述符
        - F_GETFD(void)
        - F_SETFD(long)
    * 复制文件描述符
        - F_GETFL(void)
        - F_SETFL(long)
    * 文件锁
        - F_GETLK(flock *lock) 锁定成功返回 0
        - F_SETLK, F_SETLKW
文件锁:
    位置设置为SEEK_SET，偏移为0的时候，锁整个文件


        进程
进程控制块PCB:
    内容：
        * 进程标识符 PID （非零正整数）
        * 父进程标识符 PPID（非零正整数）
        * 启动进程的用户ID UID
        * 所归属组 GID
        * 当前状态
        * 优先级
        * 资源占用

函数：
    FILE *popen(const char *command, const char *type);
        打开管道
    int pclose(FILE *stream);
        关闭管道
    command为可执行文件的全路径和执行参数
        因为管道被定义成单向的，所以type只能为读r或写w

子进程有自己的数据段，不会影响父进程的变量值
fork:
    子进程创建成功：在父进程返回子进程的PID
                    在子进程中返回 0 
    创建失败：均返回 -1

当子进程需要修改数据段时，才开始拷贝原进程的数据段，这样可以推迟甚至避免拷贝操作

pid_t wait(int *status)
pid_t waitpid(pid_t pid, int *status, int options):
    * 阻塞当前进程的运行，直到有信号到或子进程结束。
    * 返回子进程标识符。
    * status 返回子进程结束状态值，可设为NULL
    * waitpid 函数更灵活，可选择不阻塞立即返回或等N个子进程结束再返回
        pid为等待的子进程的标识符
        options：
            - WNOHANG: 如果没有已经结束的子进程，立即返回，不进行等待
            - WUNTRACED: 如果子进程也被阻塞，则立即返回

unsigned int sleep(unsigned int seconds):
    将当前进程阻塞 seconds 秒

vfork
    创建的新进程往往会调用exec函数，子进程刚开始会共享父进程的地址空间，但调用exec之后，子进程完全由exec执行的新程序替换掉，仅仅保留进程标识符不变，子进程原来的数据段和堆栈段被丢弃，重新分配新的
    与fork另一个区别，vfork保证子进程先运行，子进程调用exec或exit之后父进程才可能被调度运行。所以，如果子进程调用exec或exit之前依赖了父进程，就会死锁


        exec函数族
exec的参数
    * path: 可执行文件名的全路径名
    * file: 可以是全路径名，也可以只是执行文件名
    * arg:  可执行文件的命令行参数，可以多个，以NULL结尾
    * argv: 字符串数组：传递命令行参数的另一种形式，一般为
         - char * argv[] = {"full path", "param1","param2",...NULL}:
    * envp: 新进程的环境变量，字符串数组，一般为
         - char *envp[] = {"name1=val1","name2=val2",...NULL}:
         - 对于有参数envp的函数调用，新进程中全局变量environ指针指向的环境变量数组将被envp中的内容替代
命名规则：
    * l:list    命令行参数列表
    * v:vector  指向各命令行参数的指针数组，将数组首地址传给他
    * p:path    可执行文件在PATH环境变量的目录中搜寻
                不带p则第一个变量必须指定可执行文件的全路径 
    * e:environment 可以传入新的环境变量列表
                    如果没有，则只能使用当前的默认环境变量

输出系统的各环境变量
    char **penv = __environ;
    while(penv && *penv)
    {
        printf("%s", *penv++);
    }

终止进程
* 正常终止
    - main函数里的return
    - 调用exit()
    - 调用_exit()
    
* 异常终止
    - 调用abort()
    - 收到导致终止的信号

_exit与exit函数：
    _exit较简单，直接停止进程运行，清除内存空间，注销相关数据结构
    exit是_exit增强，还会检查文件打开情况，清理IO缓冲

每一个用f———函数打开的文件，在内存中都有一片缓冲区
例如：
    printf("ABC\n");
    printf("DEF");
    _exit();
    因为DEF后没换行符，不会打印出DEF

    printf("ABC\n");
    printf("DEF");
    exit();
    正常打印

守护进程以超级用户（UID为0）的优先级运行，父进程为init进程，没有控制终端

父进程先于子进程退出，子进程变为孤儿进程，由init进程（UID为1）收养，变为init的子进程

进程组:
    一个或多个进程的集合，有一个组长进程，进程组标识符等于组长进程的标识符，但不会因为组长进程结束而结束
会话过程:
    一个或多个进程组的集合，通常开始于用户的登录，终止于用户的退出，此期间用户运行的所有进程属于这个会话过程
    会话过程中的进程组共享一个控制终端，该控制终端通常也是创建进程的登录终端

创建守护进程：
    调用fork终止父进程、调用pid_t setsid(void)使子进程变成新的进程组和会话过程的组长进程，同时与原来的进程组和会话过程脱离。
    由于会话过程对控制终端具有独占性，子进程会脱离原控制终端的控制，所以可以关闭标准输入输出和错误输出，或者将它们重定向

chdir():改变工作目录,如chdir("/tmp"):
umask()：改变文件权限掩码

服务器会保留父进程的循环状态以接受更多并发请求，创建子进程来处理请求
    而父进程等待子进程结束会影响并发性
        不等待会使子进程变成僵尸进程
    signal(SIGCHLD,SIG_IGN);防止子进程变成僵尸进程

守护进程的输出：
    void syslog(int priority, char *format, ...);
    会写入到/var/log/syslog之中
    参数:
        * priority 写入信息的等级和用途
        * format    格式字符串，指定信息输出格式、
        