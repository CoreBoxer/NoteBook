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
文本界面下的网络设置
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

