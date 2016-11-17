#mmap
*mmap不影响源文件长度*

虚拟内存区间由：程序代码、数据、BSS、堆区、内存映射区、栈区构成
（地址由低到高）
查看方法：  /proc/pid/maps

mmap设备方法就是要建立虚拟地址到物理地址的页表
```
int (*mmap)(struct file*,struct vm_area_struct *))
```
虚拟地址转化为物理地址的函数：
    int virt_to_phys();
页表的建立：
    remap_pfn_range函数 
```
int memdev_mmap(struct file *filp,struct vm_aera_struct* vma)
{
    Vma->vm_flags |= VM_IO;
    Vma->vm_flags |= VM_RESERVED; //设置访问属性

    if(remap_pfn_range(vma,vma->vm_start,
        virt_to_phys(dev->data)<<PAGE_SHIFT,
        size,
        vma->vm_page_prot))
     return -EAGAIN;

    return 0;
} 
```
应用：通过mmap把用户空间的一段地址关联到设备内存上

##寄存器与内存

寄存器和RAM的区别在于 寄存器操作有副作用，比如清零

* IO端口：寄存器或内存位于IO空间
* IO内存：寄存器或内存位于内存空间

#IO端口操作：
1.申请
    函数：
        struct resource *reuest_region(unsignwed long first,
            unsigned long n,const char name);
    情况被记录在 /proc/ioprots中
2.访问
    IO端口分为8位、16位和32位端口。
    内核头文件定义了以下几个访问函数
    * 8位
        - unsigned inb(unsigned port)   //读
        - void outb(unsigned char byte,unsigned port) //写
    * 16位
        - unsigned inb(unsigned port)   //读
        - void outb(unsigned char byte,unsigned port) //写
    * 32位
        - unsigned inb(unsigned port)   //读
        - void outb(unsigned char byte,unsigned port) //写

3.释放
    函数：
        void release_region(unsigned long start,unsigned long n)

#IO内存口操作：
1.申请
    函数：
        struct resource *reuest_mem_region(unsignwed long first,
            unsigned long n,const char name);
        成功：返回非NULL  失败：返回NULL
    情况被记录在 /proc/iomem中
3.映射IO内存
    *在访问IO内存之前，必须进行物理地址到虚拟地址之间的映射*，ioremap函数具有此功能
    void *loremap(unsigned phys_addr,unsigned long size);
4.访问
    IO端口分为8位、16位和32位端口。
    内核头文件定义了以下几个访问函数
    * 8位
        - unsigned ioread8(void *addr)   //读
        - void iowrite8(u8 value,void *addr) //写
    * 16位
        - unsigned ioread16(void *addr)   //读
        - void iowrite16(u16 value,void *addr) //写
    * 32位
        - unsigned ioread32(void *addr)   //读
        - void iowrite32(u32 value,void *addr) //写

5.释放
    函数：
        void release_region(unsigned long start,unsigned long n)\




混杂设备
    一些字符设备共享一个主设备号：10，但次设备号不同，称为混杂设备(miscdevice)
    所有混杂设备形成一个链表，访问时内核根据次设备号查到相应设备

    struct miscdevice{
        int minor;  /*次设备号*/
        coust char *name;/*设备名*/
        const struct file_operations *fops;/*文件操作*/
        struct list_head list;
        struct device *parent;
        struct device *this_device;
    }

注册混杂设备：
    函数：
    int misc_register(struct miscdevice *misc);

##Sys文件系统
其它目录大部分设备最终都指向Devices目录下的设备
kobject

