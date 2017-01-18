X-Window界面转文本：
    Ctrl + Alt + Fx
文本界面转X-Window或文本界面：
    Alt + Fx 或 Ctrl + Alt + Fx
前四个主分区：
    /dev/sda1:boot分区放在硬盘第一分区，使BIOS最快找到内核
    /dev/sda2:根分区
    /dev/sda3:swap设为内存1倍或2倍，为“swap文件系统”
第四个会扩展为逻辑分区
    /dev/sda5:
        /tmp 和 /var/log 最好单独分区，不影响主分区，可以直接格式化清除

RAID分区：
    磁盘阵列（Redundant Arrays of Independent Disks，RAID）Redundant：冗余、过剩，有“独立磁盘构成的具有冗余能力的阵列”之意。
    磁盘阵列是由很多价格较便宜的磁盘，组合成一个容量巨大的磁盘组，利用个别磁盘提供数据所产生加成效果提升整个磁盘系统效能。利用这项技术，将数据切割成许多区段，分别存放在各个硬盘上。
大容量服务器的硬盘过渡接口可能是RAID卡
可以在多个磁盘上创建RAID分区，然后共同以mdx（md0、md1等）的方式当成整体的分区
以RAID形式并行写入，如果1个是10M速度，两个理论会达到20M速度，
（RAID Level: RAID0为最高性能，RAID1为备份模式其中一个卷会作为备份，
                RAID4会把其中一个分区作为只写奇偶校验块的分区，校验分区会成为速度瓶颈
                RAID5会把在所有分区都作为写奇偶校验块的分区，产生一次校验块数据、
                RAID6会把在所有分区都作为写奇偶校验块的分区，产生二次校验块数据）
    RAID在单个硬盘上是不会提速的，实际还是在写一个硬盘 
安装过程中：
    Ctrl + Alt + F1 命令行式安装界面
    Alt + F2 文本系统
    Alt + F3 图形系统

硬盘的第一扇区：
    512KB:
        * 前446KB:MBR（Master Boot Record）主引导记录
        * 64KB:DPT标准分区表

BootLoader加密可防止被他人进入单用户模式

双引导原理：
    Windows的引导记录装在激活分区PBR（Partion Boot Record）分区引导记录里，MBR里的446个字节会做成一个自动跳转，而Linux的引导装在MBR里，Linux在Windows寻找激活分区之前将其截断，然后供用户选择启动Linux还是继续启动Windows

设置启动方式：
    vim /etc/inittab 
文本界面下的网络设置(仅限红帽)
    system-config-network   相当于图形化改写文件
Linux下改写文件需要重启服务生效，而使用命令设置会立即生效
Windows下的设置会保存在注册表里，UNIX会使用二进制形式

RHEL5里面改完后需要 service network restart
RHEL6里面改完后需要 
    vim /etc/sysconfig/network-scripts/ifcfg-eth0
        里面设置    onboot=yes
    service NetworkManager restart
    service network restart

设置启动的命令：
    chkconfig
        eg: chkconfig network on 会打开network以后的自动启动

Linux下的BootLoader:grub
Linux的修复：
    boot命令行（RHEL6下按Esc）输入linux rescue
        chroot /mnt/sysimage
        grub-install /dev/sda来把BootLoader重新安装到sda
Windows的修复：修复模式下使用 Fixmbr 命令

Linux下寻求帮助:
* --help
* -h
* man
* info
* howto     tldp.org   (the linux document project)
* README 文件 /usr/share/doc/
* google

Ctrl + z 暂停并转入后台  按 jobs 可以显示后台
Ctrl + l 清屏
Ctrl + r 搜索命令历史
Ctrl + c 停止进程 
Ctrl + s 进入命令缓存模式  Ctrl + q 退出

stat 显示文件的状态，权限、用户、及Access访问时间，Modify内容修改，Change属性修改
touch 会更新三种时间
alias 命令别名映射
                eg: alias ll='ls -l -color=tty'
    unalias     eg: unalias ll

root 账号： UID为0

cat:全部显示

more:分页显示，按空格显示下一页

less:分页显示，可以上下翻滚，可以使用 \ 来搜索

head:显示开头十行
    -n 5 显示开头五行
tail:显示末尾十行
    -n 5 末尾开头五行
    -f:一直监视该文件

grep -R -l:文件全部筛选一遍，并只显示文件名
    -v: 反向选择
    -c: 显示行号
    -A5: 显示上5行
    -B5: 显示下5行


cut:分段显示
    如：grep max /etc/passwd | cut -d : -f7  
        显示 max 用户所使用的shell，分段时以 : 为分隔符

    cut -d : -f7 /etc/passwd
        显示所有用户使用的shell
    cut -d : -f1-3 /etc/passwd
        显示所有第1到3个段
    cut -d : -c1-3 /etc/passwd
        显示所有第1到3个字符
sort:排序
    sort -t: +2 -n /etc/passwd
        以:为分隔符，从第3列开始，以数字排序（否则默认ACSII码）
        -r 反向排序
wc: 统计行数、单词数、字符数
    -l 只显示行数
    -w 只单词数
    -c 只字符数

uniq:不显示重复

组合命令：
    grep max /etc/passwd | cut -d : -f7  | sort | uniq | wc -l
        显示有多少种shell


        账户管理
/etc/shadow 
    如：max:$1$dajfsdkgjjsdfkgh./:13822:0:99999:7:::
        $1$dajfsdkgjjsdfkgh./   存放用户的密码（MD5）单向加密，不能反推回密码，
        13822:  之后为UNIX time（1970年开发），以他作为元年，推算今天是第多少天
        0:99999: 密码修改的最短时间和最长有效时间0天，和99999天，表示永不过期
        7::     密码过期前七天通知
/etc/group  记录组和组ID
    如max:x:501:
        组名max，组密码加密，组ID:501，最后一个冒号后加用户名，可以加入该组
            但其他用户加入root组也不是管理员，管理员只是用户ID为0的，但Windows
            会把权限分配给组
/etc/group  记录组密码

/etc/login.defs 记录创建用户的默认属性

userdel -r max 可以将用户及其邮箱一起删除

usermod max    用户各种属性的修改

gpasswd -M abc,max,bac root     将多个用户abc,max,bac加入同一个组root
gpasswd -G abc,max,bac root     将一个用户root加入多个组abc,max,bac

passwd和shadow、.bashrc之类的都有备份:passwd-    shadow- .bashrc~

users   只显示当前登录的用户
who     只显示当前登录的用户及其来源
w       只显示当前登录的用户及运行的程序
write   给当前联机的用户发消息，mesg决定是否可以写消息
    eg: write root pts/0    
        mesg y 表示可以写消息
        mesg n 表示不可以写
wall(write all)    给所有登录在本机的用户广播消息
last    查看用户的登陆日志
lastlog 查看每个用户的最后登录情况
finger  查看用户信息

which 查找命令，只限于可执行文件

whichis 查找命令及帮助

locate 命令与 updatedb 命令成对，
    locate搜索数据库内的文件
    数据库在内天凌晨4:30更新一次
    任务计划：   /etc/cron.daily/

find：
    从当前目录开始查找，或指定目录
    find / -name "newfile"    从/目录寻找一个叫newfile的文件
        后面加 -ls 可以以列表形式列出来
    带执行：find /etc -name "*network*" -exec rm {} \;
        会把查找结果替换掉 {} 来直接执行
        find /etc -name "*network*" -ok rm {} \;会询问是否执行
        -perm 权限 -type 权限

history:
    命令历史

tar（打包）
    解包到其他目录 -C
    tvf 查看

    cvzf z表示gzip压缩   tar.gz
    cvzj j表示bzip2压缩  tar.bz2

    c换x 即 解压

    r将文件添加入已存在的压缩文件中


        正则表达式

. 一个字符  * 任意多个前面的字符 .* 任意多个任意的字符
a* 空、a、aa、aaa乃至更大
a? 空、a
a+ a、aa、aaa乃至更大

^ 以它开头  $ 以它结尾
匹配单词（带\<\> 的）
\<a..k\>    以a开头，中间有两个任意字母，以k结尾
\<aaa       以aaa开头

a\{18\}     a重复18次
a\{18，20\} a重复18次到20次
a\{18，\}   a重复18次以上
'^$'        表示空行

[ ]         任意一个中括号内的满足条件就行
    如：以a~h中的字母开头的 '^[a-h]'
        不以a~h中的字母开头的 '^[^a-h]'


            输入、输出及重定向

2> 把错误的消息重定向输出

cat > 文件名
    新的输入方式，只是会覆盖
cat >> 文件名
    新的输入方式，追加输入

cat << abc
    一直输入直到输入了“abc”或Ctrl + d 结束了键盘输入，然后整个输出

部分命令本身支持以文件作为输入，不支持的可以用 <

tr "a-z" "A-Z" 将输入全部转换为大写
tr "a-z" "A-Z" < /etc/passwd 将输入的文件内容全部转换为大写

检查各个网络节点的状况（掉包率、传输时间）
traceroute
mtr

检查本机状态
top ps vmstat

TCP/IP 三次握手后转为ESTABLISH状态
CC攻击：同IP进行大量访问
syn-flood：大量半握手放弃，造成许多TIMOUT

telnet IP 端口号   检查TCP/IP协议通信状态

抓包工具：
    iptraf
    tcpdump
    wireshark

ARP攻击：冒充同IP的机器，截取数据包
    方法：arping IP
        攻击的机器一般用时较少
        所以通过arp命令设置 绑定IP地址只访问较长的MAC设备
        arp -s IP MAC

sysctl -w 修改内核参数，只能本次生效
vi /etc/sysctl.conf 可以永久生效 
net.ipv4.ip_forword 启动防火墙，路由器相关的功能
net.ipv4.icmp_echo_ignore_broadcasts 启动


hostname 显示或设置主机名
uname -a 显示系统信息
last    显示最近的用户登录
lastlog 显示每个用户的登录情况

/etc/hosts
    系统自我解析的域名
永久更改主机名
    /etc/sysconfig/network

df：磁盘使用情况
du -sh：某个目录的大小

只有/proc/sys里的可以设置，其它均为只读

!$ 表示上一个命令的最后一个参数

/var/log/下
    /message 所有程序相关的信息
    /secure 安全相关的更改
    /wtmp   二进制的安全信息文件，不能直接更改，为last提供信息
    /maillog 邮箱信息
    /xferlog FTP登录日志
    /cron   自动计划任务的日志

当前每一个进程都会在/proc目录下有一个以PID为文件名的文件夹

    各类日志下的 .1 .2 .3 .4 按时间截断日志信息，系统按时清理特定时间段的日志

进程的状态标识
S Sleeping
T sTop
R Running
D Deep sleeping
Z Zombie

kill -l 显示所有信号种类

-9  SIGKILL 直接杀死，可能产生僵尸进程
-15 SIGTERM 让程序进程，可以处理后事
-19 SIGSTOP 暂停进程，此时状态变为‘T’，代表“STOP”
-18 SIGCONT 进程继续，进程继续工作

top命令下的使用
< > 翻页
M 按内存排序
k 杀死进程
n 调节进程优先级

命令后加 & ：　命令在后台进行
jobs 查看后台运行的进程

kill %1 关闭后台运行的第一个进程
bg %1 启动后台暂停的程序
fg %1 将后台的程序调到前台
Ctrl + Z 将任务暂停并加入后台

nohup 程序 & 将程序脱离控制台，一直在后台运行

killall 根据名字杀掉进程

skill 管理控制台，或某个用户的全部进程 skill -9 tty1 根据进程拥有者关闭

pkill 更精确的控制，如 pkill -u max，关闭max用户所有的进程

pstree 以树形显示所有进程

进程优先级（高->低）-20 ~ 19 

top 里通过r改变进程优先级（nice）
    k 杀死进程 
    n 页数
    q 退出
    < > 翻页

nice [优先级] [进程名] 以特定优先级执行进程
renice [新优先级] [PID] 修改进程的优先级

PATH=$PATH:/etc/init.d
    将/etc/init.d 加入PATH环境变量
~/.bash_profile bash的启动脚本，可以将启动时自动执行的东西添加进去

环境变量PS1，命令提示符的格式

set 显示所有变量
env 显示环境变量

export [变量名] 声明全局变量

在执行脚本文件时，默认在打开新的bash内执行

通配符

1.“?”
该通配符可以用来代表任意单个字符，当大家不清楚查找目标中指定位置的内容是什么的时候，就可以用“?”来代替。注意一个“?”只能代表一个未知字符。如果要查找不止一个字符，可以用多个“？”来通配表示。但是如果我们不知道到底有多少个字符，该如何使用呢？如果是这样，就必须要用到下面这个通配符了。
2.“*”
该通配符可以用来代替任意多个字符。比如我们输入“*n”，系统就会自动找出所有以“n”结尾的单词或字符集，而不管它前面有多少个字符。 
3.“<”
该通配符可以表示单词的开头。如输入“<(th)”，系统就会查找到以“th”开头得单词，如“think”或“this”，但不查找“ether”。
4.“>”
该通配符可以表示单词的结尾。如输入“(er)>”，系统会自动查找以“er”结尾的单词，如“thinker”，但不查找“interact”。
5
5.“[x1x2... ]”(x1，x2表示任意字符)
该通配符可以指定要查找该括号内（x1，x2…）的任意字符。
如输入“m[ae]n”，则系统会查找“man”和“men”。 
6
6.“[x1-x2]”(x1，x2表示任意字符)
该通配符可以设置指定范围（x1到x2之间, 包括“x1”和“x2”）内任意单个字符。如输入[r-t]ight ，则系统会查找“right”和“sight”。（即在“r”和“t”之间的任意单个字符）。需要注意的是。括号内的字符要按升序的方式来排列。如不能输入“[t-r]ight”来表示该范围。 

bash组合键
Ctrl + c 结束当前任务
Ctrl + z 当前任务暂停，并放在后台
Ctrl + s 停止屏幕输出
Ctrl + q 恢复屏幕输出
Ctrl + l 清屏
Ctrl + d 标准输入结束
Ctrl + u 删除光标前所有字符
Ctrl + k 删除光标后所有字符
Ctrl + h 删除光标前一个字符
//Ctrl + h 删除光标后一个字符
Ctrl + t 调换光标前两个字符的顺序
Ctrl + a 移动光标到最前
Ctrl + e 移动光标到最后
Ctrl + b 移动光标到前一个字符
Ctrl + f 移动光标到后一个字符
Ctrl + p 上一个命令
Ctrl + n 下一个命令
Ctrl + x 标记一个位置
Ctrl + c 清除当前输入

&  后台运行程序 
()   使用子shell, 比如 (cd ../../commlib/; make) 
$()  命令替换，和 ``的作用是一样的 
<(命令)  把命令的输出到一个临时文件 
<< HereDoc

使用举例：
比如你要在 shell 脚本中 使用 awk 脚本 
awk -f <(cat <<EOF
  /abc/{
   print $0;
} 
EOF 
)

$(())  执行整数计算 $((66/2)) 
if (( 算术运算 )) 
if [[ 字符串运算 ]]
alias 定义命令别名
dot .  或 source 命令, 在当前shell中执行脚本
exec 可以重定向当前shell的文件描述符, 或运行另一个程序。
trap 可以捕获信号
nohup 防止ssh 挂起导致的问题 
screen 可以用来保持 会话,  不受ssh的关闭影响
export 导出变量给子shell使用
tee 可以 把 输出 分流
ENV_VAR=VALUE your_program 这样可以 为这一个程序 修改它环境变量，外部shell的环境变量没有被更改
tac 倒置文件

目录跳转
cd -   快速回到前一个路径
cd  回到用户的home目录
pushd, popd, dirs 实现多目录跳转
pushd 命令用来更改您的当前目录并将其存储在堆栈中。 popd 命令用来从堆栈的顶部移除目录并使您返回该位置。 dirs 命令来显示当前目录堆栈。（dir –v –p)
pushd +n; popd +n 可以操作虚拟目录堆栈

快速跳至常用目录
     你可能已经知道$PATH变量可以列出 bash的“搜索路径”——当在当前目录不能找到请求的文件时，bash会自动搜索的目录。不过，bash也支持$CDPATH变量，当试图改变目录时该变量列出cd命令转向的目录。为了使用这个特性，我们要对$CDPATH变量赋值一个目录列表，如下面的例子所示：
bash> CDPATH='.:~:/usr/local/apache/htdocs:/disk1/backups'
bash> export CDPATH
现在，无论何时使用cd命令，bash将会检查$CDPATH列表中的所有目录来查找要转向的目录名。

特殊参数
1) $*: 代表所有参数，其间隔为IFS内定参数的第一个字元 
2) $@: 与*星号类同。不同之处在於不参照IFS 
3) $#: 代表参数数量 
4) $?: 执行上一个指令的返回值 
5) $-: 最近执行的foreground pipeline的选项参数 
6) $$: 本身的Process ID 
7) $!: 执行上一个背景指令的PID 
8) $_: 显示出最後一个执行的命令

bash快捷键
Emacs风格

ctrl+p: 方向键 上 ↑ 
ctrl+n: 方向键下 ↓ 
ctrl+b: 方向键 ← 
alt+f: 光标右移一个单词 
ctrl+f :方向键 → 
alt+b: 光标左移一个单词 
ctrl+a:光标移到行首 
ctrl+e:光标移到行尾 
ctrl+k:清除光标后至行尾的内容。 
ctrl+d: 删除光标所在字母;注意和backspace以及ctrl+h的区别，这2个是删除光标前的字符 
ctrl+r:搜索之前打过的命令。会有一个提示，根据你输入的关键字进行搜索bash的history 
ctrl+m : 输入回车 
ctrl+i : 输入tab 
ctrl+[ : 输入esc

其它 
ctrl+h:删除光标前一个字符，同 backspace 键相同。 
alt + p 非增量方式反向搜索历史 
alt + > 历史命令列表中的最后一行命令开始向前 
ctrl+u: 清除光标前至行首间的所有内容。 
ctrl+w: 移除光标前的一个单词 
ctrl+t: 交换光标位置前的两个字符 
ctrl+y: 粘贴或者恢复上次的删除 
ctrl+l:清屏，相当于clear。 
ctrl + xx 光标在行头与行尾进行跳转 
alt+r 撤销当前行的所有内容 
ctrl+z : 把当前进程转到后台运行 
ctrl+s : 锁住屏幕 
ctrl+q : 恢复屏幕 
ctrl+v key: 输入特殊字符 
alt + l 将当前光标处之后的字母转化成小写字母 
alt + u 将当前光标处之后的字母转化成大写字母 
ctrl + Alt + e 扩展命令行的内容（例如：ls  =>  ls  -l  --color=tty） 
ctrl+c:杀死当前进程, 输入模式下，中断输入的命令。 
ctrl+d:退出当前 Shell 
esc + . 快捷键可以轮询历史命令的参数或选项。 
esc + t 快捷键可以 置换前两个单词。 
输入重复字母 Esc {100} e 可以输入100个e字符

按多次{esc}可以补全 
{esc}{~}可以补全本机上的用户名 
{esc}{/}可以补全文件名 
{esc}{@}可以补全主机名,localhost可以方便地用 lo补全.


Bang Bang 历史命令
!!    重新执行上一条命令 
$$  当前bash进程的PID
!N  重新执行第N条命令。比如 !3 
!-N 重新执行倒数第N条命令。!-3 
!string  重新执行以字符串打头的命令。 比如 !vim 
!?string?  重新执行包含字符串的命令。 比如 !?test.cpp? 
!?string?%  替换为： 最近包含这个字符串的命令的参数。比如：   vim !?test.cpp?% 
!$   替换为：上一条命令的最后一个参数。比如 vim !$ 
!!string  在上一条命令的后面追加 string ，并执行。 
!Nstring  在第N条指令后面追加string，并执行。 
^old^new^  对上一条指令进行替换 
修饰

:s/old/new/  对第N条指令中第一次出现的new替换为old。 比如 vim !?test.cpp?:s/cpp/c/ 
:gs/old/new/  全部替换 
:wn  w为数字， 取指令的第w个参数. 
:p 回显命令而不是执行, 比如 !ls:p  。 这个很有用， 你可以先查看你选的命令对不对，要执行时再使用!!

Bash相关文件
     /etc/profile 设置环境变量(所有用户) 
     ~/.bash_profile 设置环境变量(当前用户) 
     ~/.bashrc 
     ~/.bash_history 
     ~/.bash_logout

history 的容量
    = HISTSIZE（缓存的容量，默认1000） + HISTFILESIZE（文件内存储的容量）

history -c 清除当前bash的命令历史

！！ 调用上一个命令
！[num]调用历史记录中的第num个命令

枚举{abc}
    如： touch {abc}{123} 会创建出a1，a2……共9个文件

cd ~user 进入user的主目录

func()
    声明函数，函数体用{}封装起来

""双引号只屏蔽空格，不屏蔽变量
‘’单引号全部屏蔽成字符串
``反引号内容被预先当成命令执行一遍

touch abc`date +%y%m%d`
    会创建一个名字叫abc161222的文件，后面接年月日
\脱义符
; 在同一行分割不同命令

[ -f /etc/passwd ]加入空格后变为判断，执行后输入 echo $? 判断上一个命令是否成功执行，返回0，说明成功执行，说明存在 /etc/passwd

[ -f /etc/passwd ] && echo ok 
    如果存在/etc/passwd文件，则执行下一句，显示ok

$[ 1 + 2 ] 表示算数表达式

（语句） 在子shell中执行的命令集合
    如：(while true; do `date` > /tmp/time; sleep 1;done)

在打开一个bash后，先后执行
/etc/profile        公用
~/.bash_profile     各用户
~/.bashrc           各用户
/etc/bashrc         公用

    bashrc         每次登陆、切换用户都执行；
    bash_profile   进入最初bash时执行
        启动时profile会调用profile.d下的*所有脚本* 
        退出时会调用 ~/.bash_logout 内的脚本

改语言，可以在~/.bashrc里修改LANG和LC_ALL的环境变量内容为"zh_CN.UTF-8"
也可以在 /etc/sysconfig/i18n 里面修改

技巧：
    编辑命令 startx 的脚本文件
    vim `which startx`

1. Sed简介  
sed 是一种在线编辑器，它一次处理一行内容。处理时，把当前处理的行存储在临时缓冲区中，称为“模式空间”（pattern space），接着用sed命令处理缓冲区中的内容，处理完成后，把缓冲区的内容送往屏幕。接着处理下一行，这样不断重复，直到文件末尾。文件内容并没有 改变，除非你使用重定向存储输出。Sed主要用来自动编辑一个或多个文件；简化对文件的反复操作；编写转换程序等。以下介绍的是Gnu版本的Sed 3.02。  
2. 定址  
可以通过定址来定位你所希望编辑的行，该地址用数字构成，用逗号分隔的两个行数表示以这两行为起止的行的范围（包括行数表示的那两行）。如1，3表示1，2，3行，美元符号($)表示最后一行。范围可以通过数据，正则表达式或者二者结合的方式确定 。  
  
3. Sed命令  
调用sed命令有两种形式：  
*  
sed [options] 'command' file(s)  
*  
sed [options] -f scriptfile file(s)  
a\  
在当前行后面加入一行文本。  
b lable  
分支到脚本中带有标记的地方，如果分支不存在则分支到脚本的末尾。  
c\  
用新的文本改变本行的文本。  
d  
从模板块（Pattern space）位置删除行。  
D  
删除模板块的第一行。  
i\  
在当前行上面插入文本。  
h  
拷贝模板块的内容到内存中的缓冲区。  
H  
追加模板块的内容到内存中的缓冲区  
g  
获得内存缓冲区的内容，并替代当前模板块中的文本。  
G  
获得内存缓冲区的内容，并追加到当前模板块文本的后面。  
l  
列表不能打印字符的清单。  
n  
读取下一个输入行，用下一个命令处理新的行而不是用第一个命令。  
N  
追加下一个输入行到模板块后面并在二者间嵌入一个新行，改变当前行号码。  
p  
打印模板块的行。  
P（大写）  
打印模板块的第一行。  
q  
退出Sed。  
r file  
从file中读行。  
t label  
if分支，从最后一行开始，条件一旦满足或者T，t命令，将导致分支到带有标号的命令处，或者到脚本的末尾。  
T label  
错误分支，从最后一行开始，一旦发生错误或者T，t命令，将导致分支到带有标号的命令处，或者到脚本的末尾。  
w file  
写并追加模板块到file末尾。  
W file  
写并追加模板块的第一行到file末尾。  
!  
表示后面的命令对所有没有被选定的行发生作用。  
s/re/string  
用string替换正则表达式re。  
=  
打印当前行号码。  
#  
把注释扩展到下一个换行符以前。  
以下的是替换标记  
*  
g表示行内全面替换。  
*  
p表示打印行。  
*  
w表示把行写入一个文件。  
*  
x表示互换模板块中的文本和缓冲区中的文本。  
*  
y表示把一个字符翻译为另外的字符（但是不用于正则表达式）  
  
4. 选项  
-e command, --expression=command  
允许多台编辑。  
-h, --help  
打印帮助，并显示bug列表的地址。  
-n, --quiet, --silent  
  
取消默认输出。  
-f, --filer=script-file  
引导sed脚本文件名。  
-V, --version  
打印版本和版权信息。  
  
5. 元字符集^  
锚定行的开始 如：/^sed/匹配所有以sed开头的行。   
$  
锚定行的结束 如：/sed$/匹配所有以sed结尾的行。   
.  
匹配一个非换行符的字符 如：/s.d/匹配s后接一个任意字符，然后是d。   
*  
匹配零或多个字符 如：/*sed/匹配所有模板是一个或多个空格后紧跟sed的行。  
[] 
匹配一个指定范围内的字符，如/[Ss]ed/匹配sed和Sed。  
[^] 
匹配一个不在指定范围内的字符，如：/[^A-RT-Z]ed/匹配不包含A-R和T-Z的一个字母开头，紧跟ed的行。  
\(..\) 
保存匹配的字符，如s/\(love\)able/\1rs，loveable被替换成lovers。  
& 
保存搜索字符用来替换其他字符，如s/love/**&**/，love这成**love**。   
\<  
锚定单词的开始，如:/\<love/匹配包含以love开头的单词的行。   
\>  
锚定单词的结束，如/love\>/匹配包含以love结尾的单词的行。   
x\{m\}  
重复字符x，m次，如：/0\{5\}/匹配包含5个o的行。   
x\{m,\}  
重复字符x,至少m次，如：/o\{5,\}/匹配至少有5个o的行。   
x\{m,n\}  
重复字符x，至少m次，不多于n次，如：/o\{5,10\}/匹配5--10个o的行。  
6. 实例  
删除：d命令  
*  
$ sed '2d' example-----删除example文件的第二行。  
*  
$ sed '2,$d' example-----删除example文件的第二行到末尾所有行。  
*  
$ sed '$d' example-----删除example文件的最后一行。  
*  
$ sed '/test/'d example-----删除example文件所有包含test的行。  
替换：s命令  
*  
$ sed 's/test/mytest/g' example-----在整行范围内把test替换为mytest。如果没有g标记，则只有每行第一个匹配的test被替换成mytest。  
*  
$ sed -n 's/^test/mytest/p' example-----(-n)选项和p标志一起使用表示只打印那些发生替换的行。也就是说，如果某一行开头的test被替换成mytest，就打印它。  
*  
$ sed 's/^192.168.0.1/&localhost/' example-----&符号表示替换换字符串中被找到的部份。所有以192.168.0.1开头的行都会被替换成它自已加 localhost，变成192.168.0.1localhost。  
*  
$ sed -n 's/\(love\)able/\1rs/p' example-----love被标记为1，所有loveable会被替换成lovers，而且替换的行会被打印出来。  
*  
$ sed 's#10#100#g' example-----不论什么字符，紧跟着s命令的都被认为是新的分隔符，所以，“#”在这里是分隔符，代替了默认的“/”分隔符。表示把所有10替换成100。  
选定行的范围：逗号  
*  
$ sed -n '/test/,/check/p' example-----所有在模板test和check所确定的范围内的行都被打印。  
*  
$ sed -n '5,/^test/p' example-----打印从第五行开始到第一个包含以test开始的行之间的所有行。  
*  
$ sed '/test/,/check/s/$/sed test/' example-----对于模板test和west之间的行，每行的末尾用字符串sed test替换。  
多点编辑：e命令  
*  
$ sed -e '1,5d' -e 's/test/check/' example-----(-e)选项允许在同一行里执行多条命令。如例子所示，第一条命令删除1至5行，第二条命令用check替换test。命令的执 行顺序对结果有影响。如果两个命令都是替换命令，那么第一个替换命令将影响第二个替换命令的结果。  
*  
$ sed --expression='s/test/check/' --expression='/love/d' example-----一个比-e更好的命令是--expression。它能给sed表达式赋值。  
从文件读入：r命令  
*  
$ sed '/test/r file' example-----file里的内容被读进来，显示在与test匹配的行后面，如果匹配多行，则file的内容将显示在所有匹配行的下面。  
写入文件：w命令  
*  
$ sed -n '/test/w file' example-----在example中所有包含test的行都被写入file里。  
追加命令：a命令  
*  
$ sed '/^test/a\\--->this is a example' example<-----'this is a example'被追加到以test开头的行后面，sed要求命令a后面有一个反斜杠。  
插入：i命令  
$ sed '/test/i\\  
new line  
-------------------------' example  
如果test被匹配，则把反斜杠后面的文本插入到匹配行的前面。  
下一个：n命令  
*  
$ sed '/test/{ n; s/aa/bb/; }' example-----如果test被匹配，则移动到匹配行的下一行，替换这一行的aa，变为bb，并打印该行，然后继续。  
变形：y命令  
*  
$ sed '1,10y/abcde/ABCDE/' example-----把1--10行内所有abcde转变为大写，注意，正则表达式元字符不能使用这个命令。  
退出：q命令  
*  
$ sed '10q' example-----打印完第10行后，退出sed。  
保持和获取：h命令和G命令  
*  
$ sed -e '/test/h' -e '$G example-----在sed处理文件的时候，每一行都被保存在一个叫模式空间的临时缓冲区中，除非行被删除或者输出被取消，否则所有被处理的行都将 打印在屏幕上。接着模式空间被清空，并存入新的一行等待处理。在这个例子里，匹配test的行被找到后，将存入模式空间，h命令将其复制并存入一个称为保 持缓存区的特殊缓冲区内。第二条语句的意思是，当到达最后一行后，G命令取出保持缓冲区的行，然后把它放回模式空间中，且追加到现在已经存在于模式空间中 的行的末尾。在这个例子中就是追加到最后一行。简单来说，任何包含test的行都被复制并追加到该文件的末尾。  
保持和互换：h命令和x命令  
*  
$ sed -e '/test/h' -e '/check/x' example -----互换模式空间和保持缓冲区的内容。也就是把包含test与check的行互换。  
7. 脚本  
Sed脚本是一个sed的命令清单，启动Sed时以-f选项引导脚本文件名。Sed对于脚本中输入的命令非常挑剔，在命令的末尾不能有任何空白或文本，如果在一行中有多个命令，要用分号分隔。以#开头的行为注释行，且不能跨行。 

awk命令
    http://www.cnblogs.com/ggjucheng/archive/2013/01/13/2858470.html

$n 指第n个参数
$# 参数的数量

交互的命令
read 读入数据给变量
echo 输入数值
printf 输出数值

可以直接使用bash命令来调用没有权限的脚本文件
bash /script    在子bash中执行
. /script       在当前的bash中执行

判断时，变量间，判断符号间，必须添加空格
[$USER=root] [ $USER=root ] 都不是判断
写成 [ $USER = root ]

方法：
    [ $USER = root ] && echo "hello root"

-v 显示程序和执行结果
-x 显示执行过的语句

if语句注意对应的then

case ... in
        case1 )
                ...
                ;;
        case2 )
                ...
                ;;
        * )
                ...
                ;;
esac

使用条件判断时，注意true为0，false为1

! 条件结果取反 **注意 ！与[ ... ] 保持一个空格**

比较运算符
相同 -eq =
不同 -ne !=
大于 -gt
小于 -lt
大于或等于 -ge
小于或等于 -le
为空 -z
不为空 -n

技巧
"" 与 `` 的嵌套
echo "seq 1 3"
    >> seq 1 3
    >> 
echo "`seq 1 3`"
    >>1
    >>2
    >>3

while [ ]
    do
        ...
    done
until [ ]
    do
        ...
    done


函数
func{
    ...
}

取消函数或变量
unset i
unset func
或写在一起
unset i func

编译安装
./configure --prefix=/usr/local/...
make
make install

RPM安装
rpm -ivh ...
rpm -V 验证包
rpm -Vp 验证包
rpm -V 验证包
rpm -Uvh ...升级（没装的安装，装了的升级）
rpm -Fvh ...升级 更新（没装的也不装）
rpm -ivh --force ... （强制安装）重装
rpm -e ...卸载
rpm -e --nodeps ... 忽略依赖的卸载

rpm -qa | grep ... 可以查看安装的包
rpm -ql ...     列表
rpm -qi ...     包的详细信息
rpm -q --scripts ...查看安装时的系统设置信息
加-p可以查看包的


Ctrl + Del 关闭XWindow

启动第n个图形界面
startx -- :n-1 注意空格

几种启动方式：

    startx命令
    第一步：启动X （为X-Server） x.org
    第二步：xinit （启动xterm）
    第三步：startkde （为X-Client） 

    第一步：xinit，相当于xhost+，即允步许所所有机器输出到这里
    第二步：1.其他Linux机器上 export DISPLAY="第一台机器的IP:0"
           2. startkde，会将KDE输出在第一台机器上

    第一步：Windows 上运行 X-Manager （相当于Linux上的X-server）
    第二步：1.其他Linux机器上 export DISPLAY="第一台windows机器的IP:0"
           2. startkde，会将KDE输出在第一台机器上

进程有问题，可以删除/var/run/下的.pid文件，再重启

/etc/sysconfig/desk里设置启动的DE

gimp Gnome中的PhotoShop
display 图像阅览器
convert 图像格式转换工具


无人值守安装
图形化配置
system-config-kickstart

启动时：
    boot:linux ks=nfs:192.168..:/var/ftp/ks.cfg
nfs输入绝对路径，ftp可以是相对路径

screen 多窗口管理器

screen -dr 断开当前窗口连接，并继续执行进程
screen -r 装载保存的所有窗口

ctrl + a + d 离开当前窗口（只有以screen ... 的形式运行某程序的时候才能用）
ctrl + a + n 在多个窗口间切换
ctrl + a + \ 关闭所有窗口，此时被关闭的运行中screen窗口会无响应

另一种方式：
    screen 某程序
    ctrl + a + d，此时当前窗口断开连接（Detached），程序进入后台继续进行
    另一平台 screen -r ，重新装载刚才的程序运行窗口

第三种方式
    screen，会单独打开一个bash
    执行程序
    screen -rd 或 ctrl + a + d，（Detached）
    另一窗口screen -r


查看内核（kernel)日志:
    命令dmesg   --查看存放在/var/log/dmesg 文件中的内核日志（它由klogd维护）

/var/log/messages /var/log/secure /var/log/maillog 
/var/log/cron  /var/log/spooler
    均由 syslogd 来维护，它在/etc/logrotate.d/syslog 配置

logger：可以在shell或脚本里向/var/log/message里写入日志

计划任务

crond 定时执行
anacron 把cron未执行的任务补执行

用户：crontab -e
系统 /etc/cron.*

格式：前五位放时间，单位由小到大 min hour day month week
    如：每周周日05:01，执行重启
        1 5 * * 7 /sbin/reboot
dump 备份工具 可识别分区
    nuf 通过比较来确定要备份的文件
    0uf 全部备份
    >=之前的uf，比较比它较小的一次uf
        如3uf会和2uf比较，选取差量来备份

at 只执行一次任务
非常频繁的执行
    sysstat程序
vi /etc/cron.d/... <= 脚本文件名，权限必须为600，内容中注意需要加用户名

        grub
Linux内核文件 /boot/vmlinuz
配置文件：RedHat上：grub.conf（为了符合规范）
        正常编译后：1stmenu
    stage1 512字节 用来填入mbr来引导
    stage1 stage1_6_ext3 丢失后：grub-install
    stage2 丢失： 光盘rescue模式，chroot /mnt/sysimage   rpm -ivh grub...rpm
    配置文件丢失： 显示 grub> ,此时手动输入启动命令

