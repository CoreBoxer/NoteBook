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