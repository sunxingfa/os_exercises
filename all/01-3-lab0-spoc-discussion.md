# lab0 SPOC思考题

## 个人思考题

---

能否读懂ucore中的AT&T格式的X86-32汇编语言？请列出你不理解的汇编语言。
- [x]  

>  http://www.imada.sdu.dk/Courses/DM18/Litteratur/IntelnATT.htm
   答：在上面这个文库的帮助下，对于AT&T格式的X86-32汇编语言中的基本指令能够理解了，不过那些特殊寄存器的含义并不清楚，还有一些语句不能看懂。像：
   lgdt gdtdesc
   ljmp $PROT_MODE_CSEG, $protcseg

虽然学过计算机原理和x86汇编（根据THU-CS的课程设置），但对ucore中涉及的哪些硬件设计或功能细节不够了解？
- [x]  

> 答：存储这一块不太了解。  


哪些困难（请分优先级）会阻碍你自主完成lab实验？
- [x]  

> 答：对我来说最困难的地方是理解实验框架，也就是源代码的阅读，这一块可能涉及一些自己不了解的知识，但是又是必须弄懂的，否则无法进一步进行实验；其次是各种实验工具的上手、掌握，可能会让我花比较多的时间；最后一点是汇编代码的阅读。  

如何把一个在gdb中或执行过程中出现的物理/线性地址与你写的代码源码位置对应起来？
- [x]  

> 答：gdb中通过li指令可以将物理地址与代码位置对应起来。

了解函数调用栈对lab实验有何帮助？
- [x]  

> 答：通过函数调用栈，我们可以更好地理解在lab试验中理解操作系统的工作原理，从而在做实验的过程中更得心应手。  

你希望从lab中学到什么知识？
- [x]  

> 答：了解掌握一个操作系统的基本框架；设计实现操作系统需要掌握的基本工具；实现一个大项目所要注意的细节。  

---

## 小组讨论题

---

搭建好实验环境，请描述碰到的困难和解决的过程。
- [x]  

> 答：安装virtualbox的时候笔记本直接蓝屏，如此反复了好几次都是这样，上网查了一下原因，大体上说是因为之前安装到一半电脑蓝屏，所以重启再安装时注册表等项目存在冲突，全部删掉之后还是没有解决问题，不过最后重新下载了一次安装包，居然就莫名其妙地成功了！virtualbox安装成功之后，后面就一马平川没再出问题了。

熟悉基本的git命令行操作命令，从github上的[ucore git repo](http://www.github.com/chyyuu/ucore_lab)下载ucore lab实验
- [x]  

> 

尝试用qemu+gdb（or ECLIPSE-CDT）调试lab1
- [x]  

> 

对于如下的代码段，请说明”：“后面的数字是什么含义
```
/* Gate descriptors for interrupts and traps */
struct gatedesc {
    unsigned gd_off_15_0 : 16;        // low 16 bits of offset in segment
    unsigned gd_ss : 16;            // segment selector
    unsigned gd_args : 5;            // # args, 0 for interrupt/trap gates
    unsigned gd_rsv1 : 3;            // reserved(should be zero I guess)
    unsigned gd_type : 4;            // type(STS_{TG,IG32,TG32})
    unsigned gd_s : 1;                // must be 0 (system)
    unsigned gd_dpl : 2;            // descriptor(meaning new) privilege level
    unsigned gd_p : 1;                // Present
    unsigned gd_off_31_16 : 16;        // high bits of offset in segment
};
```
- [x]  

> 答：表示变量所占比特位。

对于如下的代码段，
```
#define SETGATE(gate, istrap, sel, off, dpl) {            \
    (gate).gd_off_15_0 = (uint32_t)(off) & 0xffff;        \
    (gate).gd_ss = (sel);                                \
    (gate).gd_args = 0;                                    \
    (gate).gd_rsv1 = 0;                                    \
    (gate).gd_type = (istrap) ? STS_TG32 : STS_IG32;    \
    (gate).gd_s = 0;                                    \
    (gate).gd_dpl = (dpl);                                \
    (gate).gd_p = 1;                                    \
    (gate).gd_off_31_16 = (uint32_t)(off) >> 16;        \
}
```
如果在其他代码段中有如下语句，
```
unsigned intr;
intr=8;
SETGATE(intr, 0,1,2,3);
```
请问执行上述指令后， intr的值是多少？
- [x]  

> 答：261683767476226。

请分析 [list.h](https://github.com/chyyuu/ucore_lab/blob/master/labcodes/lab2/libs/list.h)内容中大致的含义，并能include这个文件，利用其结构和功能编写一个数据结构链表操作的小C程序
- [x]  

> 答：
 #include <list.h>
 #include <stdio.h>

  typedef struct {
     list_entry_t free_list;
     unsigned int nr_free;
   }free_area_t;

   struct page {

     int test;
     list_entry_t page_link;
  };

   int main(){

      free_area_t free_area;
      list_entry_t* le = &free_area.free_list;
      while ( (le = list_next(le)) != free_area.free_list ) {
          printf ( "%s", "not empty" );
       }
       return 0;
   }

---

## 开放思考题

---

是否愿意挑战大实验（大实验内容来源于你的想法或老师列好的题目，需要与老师协商确定，需完成基本lab，但可不参加闭卷考试），如果有，可直接给老师email或课后面谈。
- [x]  

>  

---
