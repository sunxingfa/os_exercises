#lec 3 SPOC Discussion

## 第三讲 启动、中断、异常和系统调用-思考题

## 3.1 BIOS
 1. 比较UEFI和BIOS的区别。<br />
 > 答：
    第一，UEFI具备文件系统的支持，它能够直接读取FAT分区中的文件，但是BIOS并不具备；启动操作系统时，UEFI不需要主引导记录，不      需要活动分区，但是BIOS要从硬盘上指定扇区读取系统启动代码，然后从活动分区中引导启动操作系统；UEFI允许植入硬件驱动；UEFI运     行的更快;UEFI对新硬件有很好的支持，而BIOS由于空间有限，响应能力较差。
 1. 描述PXE的大致启动流程。<br />
 > 答：
    经网上查阅资料了解到：PXE是动态路由。其通信协议采用TCP/IP，与Internet连接高效而可靠，PXE无盘工作站的启动过程分析如下：
    1.客户端个人电脑开机后，在TCP/IP Bootrom 获得控制权之前先做自我测试。
    2.Bootprom 送出BOOTP/DHCP要求以取得 IP。
    3.如果服务器收到个人电脑所送出的要求，就会送回BOOTP/DHCP回应，内容包括客户端的IP地址、预设网关、开机映像文件。否则，服务器会忽略这个要求。
    4.Bootprom 由 TFTP 通讯协议从服务器下载开机映像文件。
    5.个人电脑通过这个开机映像文件开机， 这个开机文件可以只是单纯的开机程式也可以是操作系统。
    6.开机映像文件将包含kernel loader及压缩过的kernel，此kernel将支持NTFS root系统。
    7.远程客户端根据下载的文件启动机器。

## 3.2 系统启动流程
 1. 了解NTLDR的启动流程。<br />
 > 答：
    经网上查阅资料了解到：NTLDR文件是一个隐藏的、只读的系统文件，位置在系统盘的根目录，用来装载操作系统。一般情况系统的引导过程是这样的：
  1、电源自检程序开始运行
  2、主引导记录被装入内存，并且程序开始执行
  3、活动分区的引导扇区被装入内存
  4、NTLDR从引导扇区被装入并初始化
  5、将处理器的实模式改为32位平滑内存模式
  6、NTLDR开始运行适当的小文件系统驱动程序。小文件系统驱动程序是建立在NTLDR内部的，它能读FAT或NTFS。
  7、NTLDR读boot.ini文件
  8、NTLDR装载所选操作系统。如果windows NT/windows 2000/windows XP/windows server 2003这些操作系统被选择，NTLDR运行Ntdetect。
对于其他的操作系统，NTLDR装载并运行Bootsect.dos然后向它传递控制。windows NT过程结束。
  9.Ntdetect搜索计算机硬件并将列表传送给NTLDR，以便将这些信息写进\\HKE Y_LOCAL_MACHINE\HARDWARE中。
  10.然后NTLDR装载Ntoskrnl.exe，Hal.dll和系统信息集合。
  11.Ntldr搜索系统信息集合，并装载设备驱动配置以便设备在启动时开始工作
  12.Ntldr把控制权交给Ntoskrnl.exe，这时,启动程序结束,装载阶段开始。
 1. 了解GRUB的启动流程。<br />
 > 答：
   经网上查阅资料了解到，GRUB启动流程一般如下：
   1、系统加电后，BIOS进行检测系统参数 
   2、装载基本的引导装载程式stage1，用来装载第二引导程式
   3、装载第二引导程式，以便用户选择允许载入一个特定的操作系统，这步通常是用户显示一个菜单或者是输入命令等等 （stage2因为比较大，所以处于文件系统中）
   4、装载在一个特定分区上的操作系统

 1. 比较NTLDR和GRUB的功能有差异。<br />
 >  答：ntldr兼容性差，只能引导win,只能装在硬盘;grub是第三方操作系统引导器，可以引导多种介质和操作系统。
 1. 了解u-boot的功能。
 >  答：经网上查阅资料了解到，U-Boot可支持的主要功能如下：
    1.系统引导支持NFS挂载、RAMDISK(压缩或非压缩)形式的根文件系统；支持NFS挂载、从FLASH中引导压缩或非压缩系统内核
    2.基本辅助功能强大的操作系统接口功能；可灵活设置、传递多个关键参数给操作系统，适合系统在不同开发阶段的调试要求与产品发布，尤以Linux支持最为强劲；支持目标板环境参数多种存储方式，如FLASH、NVRAM、EEPROM
    3.CRC32校验可校验FLASH中内核、RAMDISK镜像文件是否完好
    4.设备驱动串口、SDRAM、FLASH、以太网、LCD、NVRAM、EEPROM、键盘、USB、PCMCIA、PCI、RTC等驱动支持
    5.上电自检功能SDRAM、FLASH大小自动检测；SDRAM故障检测；CPU型号
    6.特殊功能XIP内核引导
## 3.3 中断、异常和系统调用比较
 1. 举例说明Linux中有哪些中断，哪些异常？
 > 答：
    中断是指在CPU正常运行期间，由于内外部事件或由程序预先安排的事件引起的CPU暂时停止正在运行的程序，转而为该内部或外部事件或    预先安排的事件服务的程序中去，服务完毕后再返回去继续运行被暂时中断的程序。如插入外设引发的硬中断、CPU引发的同步中断、软中    断等等； 异常，就是可以打断CPU正常运行流程的一些事情，比如外部中断，未定义的指令，试图修改只读的数据，执行swi指令，Softwar    e Interrupt Instruction，软件中断指令等。当这些事情发生时，CPU暂停当期的程序，先处理异常事件，然后再继续处理执行被中断的程    序。

 1. Linux的系统调用有哪些？大致的功能分类有哪些？  (w2l1)
 > 答：
 一、进程管理：有四五十个，包括创建新进程、终止进程、创建子进程、设置或获取调度优先级等等；
 二、文件操作：四十个左右，有文件读写操作（打开、创建、读、写等等），有文件系统操作（创建目录、删除目录、改变当前目录、获取当前目录等等）；
 三、系统控制：二十多个，如获取与设置系统资源上限、打开与关闭交换文件和设备等等；
 四、内存管理：十几个，如内存页面加锁与解锁、改编数据段空间分配等等；
 五、网络管理：六七个，如取域名、设置域名、获取与设置主机标识号等等；
 六、socket控制：二十个左右，如建立socket、绑定socket、监听socket、响应socket等等；
 七、用户管理：二十个左右，如获取与设置用户标识号、获取与设置组标识号等等；
 通过上面可以看到，Linux的系统调用数量有一百多个（包括一些引申出去的），涵盖了进程管理、文件操作、内存管理、系统控制、网络管理、socket控制、用户管理等类别。

```
  + 采分点：说明了Linux的大致数量（上百个），说明了Linux系统调用的主要分类（文件操作，进程管理，内存管理等）
  - 答案没有涉及上述两个要点；（0分）
  - 答案对上述两个要点中的某一个要点进行了正确阐述（1分）
  - 答案对上述两个要点进行了正确阐述（2分）
  - 答案除了对上述两个要点都进行了正确阐述外，还进行了扩展和更丰富的说明（3分）
 ```
 
 1. 以ucore lab8的answer为例，uCore的系统调用有哪些？大致的功能分类有哪些？(w2l1)
 > 答：
    uCore的系统调用有22个。大致的功能分类有进程管理、文件操作、内存管理等，如下所示:
   1.文件操作: sys_open sys_close sys_read sys_write sys_seek sys_fstat sys_fsync sys_getcwd sys_getcwd sys_getdirentry     sys_dup
   2.进程管理: sys_exit sys_fork sys_wait sys_exec sys_yield sys_kill sys_getpid sys_putc sys_pgdir sys_gettime   sys_lab6_set_priority sys_sleep

 ```
  + 采分点：说明了ucore的大致数量（二十几个），说明了ucore系统调用的主要分类（文件操作，进程管理，内存管理等）
  - 答案没有涉及上述两个要点；（0分）
  - 答案对上述两个要点中的某一个要点进行了正确阐述（1分）
  - 答案对上述两个要点进行了正确阐述（2分）
  - 答案除了对上述两个要点都进行了正确阐述外，还进行了扩展和更丰富的说明（3分）
 ```
 
## 3.4 linux系统调用分析
 1. 通过分析[lab1_ex0](https://github.com/chyyuu/ucore_lab/blob/master/related_info/lab1/lab1-ex0.md)了解Linux应用的系统调用编写和含义。(w2l1)
 > 
    答：objdump命令是Linux下的反汇编目标文件或者可执行文件的命令。
   objdump -f lab1-ex0.exe:显示lab1-ex0.exe的文件头信息
   objdump -d lab1-ex0.exe:显示lab1-ex0.exe的需要执行指令的那些section
   objdump -D lab1-ex0.exe:显示lab1-ex0.exe的所有section
   objdump -h lab1-ex0.exe:显示lab1-ex0.exe的SectionHeader信息
   objdump -x lab1-ex0.exe:显示lab1-ex0.exe的全部Header信息
   objdump -s lab1-ex0.exe:除了显示lab1-ex0.exe的全部Header信息，还显示他们对应的十六进制文件代码
    nm命令显示关于指定 File 中符号的信息，文件可以是对象文件、可执行文件或对象文件库，如果文件没有包含符号信息，nm命令报告该情况，但不把它解释为出错条件，nm命令缺省情况下报告十进制符号表示法下的数字值。nm命令显示如下信息：库或对象名、符号名称、符号类型、值、大小、标志。命令格式一般为：nm option filename。随着中间的参数不同的话，输出的结果也不同：
   A Global absolute 符号
   a Local absolute 符号
   B Global bss 符号
   b Local bss 符号
   D Global data 符号
   d Local data 符号
   f 源文件名称符号
   T Global text 符号
   t Local text 符号
   U 未定义符号
   file用于检查文件类型，或检查块文件内部并获得文件系统信息。
   --help 显示帮助信息
   -v,--version 输出版本信息并退出
   -b,--brief 不显示文件名字，只显示文件类型。在shell脚本中时有用
   -f,--files-fromFILE 读取待测试的名称文件
   -F,--seperatorSTRING 使用字符串作为分隔符，不再使用“：”
   -i,--mime 显示文件的mime类型
   -L,--dereference 显示符号链接所指向文件信息
   -d,--debug 输出调试信息
   系统调用：指运行在用户态的程序向操作系统内核请求需要更高权限运行的服务。它是用户态进入内核态的唯一窗口，提高了系统的安全性，并且使得编程从硬件设备的低级编程中解放出来。Linux中实现系统调用用了I386体系结构中的软件中断，由内核函数实现。

 ```
  + 采分点：说明了objdump，nm，file的大致用途，说明了系统调用的具体含义
  - 答案没有涉及上述两个要点；（0分）
  - 答案对上述两个要点中的某一个要点进行了正确阐述（1分）
  - 答案对上述两个要点进行了正确阐述（2分）
  - 答案除了对上述两个要点都进行了正确阐述外，还进行了扩展和更丰富的说明（3分）
 
 ```
 
 1. 通过调试[lab1_ex1](https://github.com/chyyuu/ucore_lab/blob/master/related_info/lab1/lab1-ex1.md)了解Linux应用的系统调用执行过程。(w2l1)
 > 答：
  strace常用来跟踪进程执行时的系统调用和所接收的信号,strace可以跟踪到一个进程产生的系统调用,包括参数，返回值，执行消耗的时间。
gcc -o lab1-ex1.exe lab1-ex1.c返回的结果是基于时间排序的，不符合要求。
本次调用的命令为: gcc -tt lab1-ex1.exe lab1-ex1.c，返回的结果时基于时间的：

21:54:15.477561 execve("./lab1-ex1.exe", ["./lab1-ex1.exe"], [/* 63 vars */]) = 0
21:54:15.478729 brk(0) = 0x92b000 21:54:15.479053 access("/etc/ld.so.nohwcap", F_OK) = -1 ENOENT (No such file or directory)
21:54:15.490855 mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f1d3daa0000
21:54:15.490937 access("/etc/ld.so.preload", R_OK) = -1 ENOENT (No such file or directory)
21:54:15.490994 open("/home/yangjun/ns2/ns-allinone-2.35/otcl-1.14/tls/x86_64/libc.so.6", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
21:54:15.491044 stat("/home/yangjun/ns2/ns-allinone-2.35/otcl-1.14/tls/x86_64", 0x7ffff184a1d0) = -1 ENOENT (No such * file or directory)
21:54:15.491082 open("/home/yangjun/ns2/ns-allinone-2.35/otcl-1.14/tls/libc.so.6", O_RDONLY|O_CLOEXEC) = -1 ENOENT * (No such file or directory)
21:54:15.491116 stat("/home/yangjun/ns2/ns-allinone-2.35/otcl-1.14/tls", 0x7ffff184a1d0) = -1 ENOENT (No such file or directory)
21:54:15.491148 open("/home/yangjun/ns2/ns-allinone-2.35/otcl-1.14/x86_64/libc.so.6", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
21:54:15.491182 stat("/home/yangjun/ns2/ns-allinone-2.35/otcl-1.14/x86_64", 0x7ffff184a1d0) = -1 ENOENT (No such file or directory)
21:54:15.491214 open("/home/yangjun/ns2/ns-allinone-2.35/otcl-1.14/libc.so.6", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
21:54:15.491248 stat("/home/yangjun/ns2/ns-allinone-2.35/otcl-1.14", {st_mode=S_IFDIR|0755, st_size=4096, ...}) = 0
21:54:15.491287 open("/home/yangjun/ns2/ns-allinone-2.35/lib/tls/x86_64/libc.so.6", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
21:54:15.491321 stat("/home/yangjun/ns2/ns-allinone-2.35/lib/tls/x86_64", 0x7ffff184a1d0) = -1 ENOENT (No such file or directory)
21:54:15.491354 open("/home/yangjun/ns2/ns-allinone-2.35/lib/tls/libc.so.6", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
21:54:15.491387 stat("/home/yangjun/ns2/ns-allinone-2.35/lib/tls", 0x7ffff184a1d0) = -1 ENOENT (No such file or directory)
21:54:15.491419 open("/home/yangjun/ns2/ns-allinone-2.35/lib/x86_64/libc.so.6", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
21:54:15.491452 stat("/home/yangjun/ns2/ns-allinone-2.35/lib/x86_64", 0x7ffff184a1d0) = -1 ENOENT (No such file or directory)
21:54:15.491484 open("/home/yangjun/ns2/ns-allinone-2.35/lib/libc.so.6", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
21:54:15.491517 stat("/home/yangjun/ns2/ns-allinone-2.35/lib", {st_mode=S_IFDIR|0755, st_size=4096, ...}) = 0
21:54:15.491554 open("/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
21:54:15.491590 fstat(3, {st_mode=S_IFREG|0644, st_size=93203, ...}) = 0
21:54:15.491622 mmap(NULL, 93203, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7f1d3da89000
21:54:15.491655 close(3) = 0
21:54:15.491688 access("/etc/ld.so.nohwcap", F_OK) = -1 ENOENT (No such file or directory)
21:54:15.491730 open("/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3
21:54:15.491767 read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\320\37\2\0\0\0\0\0"..., 832) = 832
21:54:15.491805 fstat(3, {st_mode=S_IFREG|0755, st_size=1845024, ...}) = 0
21:54:15.491844 mmap(NULL, 3953344, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7f1d3d4ba000
21:54:15.491878 mprotect(0x7f1d3d675000, 2097152, PROT_NONE) = 0
21:54:15.491925 mmap(0x7f1d3d875000, 24576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x1bb000) = 0x7f1d3d875000
21:54:15.491981 mmap(0x7f1d3d87b000, 17088, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7f1d3d87b000
21:54:15.492031 close(3) = 0
21:54:15.492082 mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f1d3da88000
21:54:15.492129 mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f1d3da86000
21:54:15.492178 arch_prctl(ARCH_SET_FS, 0x7f1d3da86740) = 0
21:54:15.492391 mprotect(0x7f1d3d875000, 16384, PROT_READ) = 0
21:54:15.492522 mprotect(0x600000, 4096, PROT_READ) = 0
21:54:15.492578 mprotect(0x7f1d3daa2000, 4096, PROT_READ) = 0
21:54:15.492624 munmap(0x7f1d3da89000, 93203) = 0
21:54:15.492720 fstat(1, {st_mode=S_IFCHR|0620, st_rdev=makedev(136, 10), ...}) = 0
21:54:15.492823 mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f1d3da9f000
21:54:15.492887 write(1, "hello world\n", 12hello world ) = 12
21:54:15.492993 exit_group(12) = ?
21:54:15.493095 +++ exited with 12 +++ 在本次系统调用中，分别调用了mmap,open,access,mprotect,munmap,brk,fstat,write,read,close,execve,arch_prctil这些过程。
mmap将一个文件或者其它对象映射进内存
open 函数用于打开和创建文件
access判断是否具有存取文件的权限
mprotect: 设置内存访问权限
munmap删除特定地址区域的对象映射
brk实现虚拟内存到内存的映射
fstat由文件描述词取得文件状态
write系统调用 
Linux内核会先进行若干检查，接着将数据复制进缓冲区。稍后，内核会在后台收集所有“脏”(有数据写入)缓冲区(内容跟相应磁盘块不同的所有缓冲区)，将它们安排成最佳顺序，接着写进磁盘。read函数从打开的设备或文件中读取数据,close关闭文件,execve运行可执行文件,arch-prctil设置架构特定的线程状态,这样就完成了系统调用的过程。

 ```
  + 采分点：说明了strace的大致用途，说明了系统调用的具体执行过程（包括应用，CPU硬件，操作系统的执行过程）
  - 答案没有涉及上述两个要点；（0分）
  - 答案对上述两个要点中的某一个要点进行了正确阐述（1分）
  - 答案对上述两个要点进行了正确阐述（2分）
  - 答案除了对上述两个要点都进行了正确阐述外，还进行了扩展和更丰富的说明（3分）
 ```
 
## 3.5 ucore系统调用分析
 1. ucore的系统调用中参数传递代码分析。
 1. ucore的系统调用中返回结果的传递代码分析。
 1. 以ucore lab8的answer为例，分析ucore 应用的系统调用编写和含义。
 1. 以ucore lab8的answer为例，尝试修改并运行代码，分析ucore应用的系统调用执行过程。
 
## 3.6 请分析函数调用和系统调用的区别
 1. 请从代码编写和执行过程来说明。
   1. 说明`int`、`iret`、`call`和`ret`的指令准确功能
 
