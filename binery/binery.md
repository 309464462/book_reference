---
typora-root-url: img
---

### 第一章 Linux环境和相关工具

> 二进制逆向工程的通用软件IDA Pro

#### 1.1 LInux工具

##### 1.1.1 GDB
> GDB 不仅可以用来调试有BUG的应用程序，也可以用于研究甚至改变一个程序的控制流，还可以用于修改代码、寄存器和数据结构。主要用于分析ELF文件和Linux进程。

##### 1.1.2 GNU binutils中的objdump

> 一种对代码进行快速反编译的方式。起缺点是需要依赖ELF文件头，并且不会进行控制流分析。

- 查看ELF文件中的所有节的数据或代码

  ==objdump -D  <elf file>==

- 只查看ELF文件中的程序代码

  ==objdump -d  <elf file>==

- 查看所有符号

  ==objdump -tT <elf file>==

##### 1.1.3 GNU binutils中的objcopy

> 可以用来分析和修改任意类型的ELF目标文件，还可以修改ELF节，或将ELF节复制到ELF二进制中。

###### **要将.data 节从一个eldf目标文件复制到另一个elf文件中，可以使用下面**

==objcopy -only-section=.data <infile> <outfile>==

#### 1.1.4 strace

> 系统调用追踪

- 使用strace 命令来跟踪一个基本的程序：

 ==strace  /bin/ls -o ls.out==

- 使用strace 命令附加到一个现存的进程上，查看log

 ==strace -p <pid> -o demon.out==  

- 原始输出将会显示每个系统调用的文件描述符编号，系统调用会将文件描述符作为参数

  SY_read(3,buf,sizeof(buf))

  如果想看读入到文件描述符3中的所有数据，可以运行下面的命令

  ==strace -e read=3 /bin/ls==

  ==strace -e write=fd /bin/ls==

##### 1.1.5 ltrace

> 库追踪，它会分析共享库，即一个程序的链接信息，并打印出用到的库函数

##### 1.1.6 基本的ltrace命令
> 除了可以查看库函数调用之外，还可以使用-S 标记查看系统调用。ltrace命令通过解析可执行文件的动态段，并打印出共享库和静态库的实际符号和函数，来提供更细粒度的信息

   ==ltrace <program> -o program.out  //针对运行的程序

![微信图片_20180717223634](E:\mybook\binery\img\微信图片_20180717223634.png)

##### 1.1.7 ftrace

> 作者自己写的 在https://github.com/elfmaster/ftrace

##### 1.1.8readelf

> 在进行反编译之前，需要收集目标文件相关的信息，该命令能够提供收集信息所需要的特定于elf的所有数据。

- 查询节表头

  readelf -S <object>

- 查询程序头表

  readelf -l <object>

- 查询符号表

  readelf -s <object>

- 查询ELF文件头数据

  readelf -n <object>

- 查询重定位入口

  readelf -r <object>

- 查询动态段

  readelf -d <object>



##### 1.1.9 ERESI-ELF反编译系统的接口

> (http://www.eresi-project.org) 中含有许多linux二进制工具                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                

#### 1.2 有用的设备和文件

> /proc入口对于黑客是很有用的

##### 1.2.1 /proc/<pid>/maps

> 这个文件保存了一个进程镜像的布局，通过展现每个内存映射来实现，展现的内容包括可执行文件，共享库、栈、堆和VDSO等。

![微信图片_20180717223653](/微信图片_20180717223653.png)

![微信图片_20180717223718](/微信图片_20180717223718.png)

#### 1.2.2 /proc/kcore

> 内核的动态核心文件
>
> GDB可以使用/proc/kcore来对内核进行调试和分析。

##### 1.2.3 /boot/System.map

> 包含了整个内核的所有符号

![微信图片_20180717223750](/微信图片_20180717223750.png)

##### 1.2.4 /proc/kallsyms

> 和System.map类似，其可以动态更新，如果动态安装内核模块，符号会自动添加到/proc/kallsyms中

##### 1.2.5 /proc/iomem

> 与/proc/<pid>/maps类似，不过他是跟系统内存相关的。例如想知道内核的text段所映射的物理内存位置，可以搜索kernel字符串，然后可以查看code/text段 、data段和bss段的相关内容

![微信图片_20180717223550](/微信图片_20180717223550.png)

##### 1.2.6 ECFS

> (extened core file snapshot) 扩展核心文件快照 是一项特殊的核心转储技术，专门为进程镜像的高级取证分析所设计。

#### 1.3 链接器相关环境指针

> 动态加载器/链接器以及链接的概念，在程序链接、执行的过程中都是避不开的基本组成部分。需要特别理解：链接、重定向、动态加载的过程

##### 1.3.1 LD_PRELOAD环境变量

> 可以设置成一个指定库的路径，动态链接时候可以比其他的库有更高的优先级。这就允许预加载苦衷的函数和符号能够覆盖掉后续链接的库中的函数和符号。这在本质上就允许你通过重定向共享库函数来进行运行时修复。

##### 1.3.2 LD_SHOW_AUXV环境变量

> 这个环境变量可以通知程序加载器来展示程序运行时的辅助向量。辅助向量是放在程序栈上的信息。例如：想要获取进程镜像VDSO页的内存地址（也可以通过maps文件获取），就需要查询AT_SYSINFO

![微信图片_20180717225605](/微信图片_20180717225605.png)



##### 1.3.3 链接器脚本

> 链接器脚本是由链接器解释的，把程序划分为相应的节、内存和符号。默认链接器脚本可以使用
>
> ==ld -verbose==   查看 版本和具体内容

![微信图片_20180717230233](/微信图片_20180717230233.png)



gcc  可以通过 -T 标志来指定链接脚本

### 第二章 二进制格式

> 将会涉及到 ELF文件类型、程序头、节头、符号、重定位、动态链接、编码ELF解析器

#### 2.1 ELF文件类型

一个ELF文件可以标记为以下几种类型之一

- ET_NONE:未知类型。
- ET_REL:重定位文件。ELF类型标记为relocatable 意味着该文件被标记为了一段可重定位的代码，有时也称目标文件。可重定位目标文件通常是还未被链接到可执行程序中的一段位置独立的代码（也就是解析阶段的文件）。在编译完代码之后通常可以看到一个.o格式的文件，这种文件包含了创建可执行文件所需要的代码和数据。
- ET_EXEC:可执行文件。ELF类型为executable，表明这个文件标记为可执行文件。这种类型的文件也称为程序，是一个进程开始执行的入口。
- ET_DYN：共享目录文件。ELF类型为dynamic，意味着该文件被标记为了一个动态的可链接的目标文件，也称为 共享库。这种共享库会在程序运行时被转载并链接到程序的进程镜像中。
- ET_CORE:核心文件。在程序崩溃或者进程传递了一个SIGSEGV信号（分段违规，一般是内存违规访问导致的）时，会在核心文件中记录整个进程的镜像信息。可以使用GDB读取这类文件来辅助调试并查找程序崩溃的原因。

使用readelf  -h 命令查看ELF文件，可以看到原始的ELF文件头。ELF文件头从文件的0偏移量开始，是 除了文件头之后剩余部分文件的一个映射。

```c
 typedef struct {
               unsigned char e_ident[EI_NIDENT];
               uint16_t      e_type;
               uint16_t      e_machine;
               uint32_t      e_version;
               ElfN_Addr     e_entry;
               ElfN_Off      e_phoff;
               ElfN_Off      e_shoff;
               uint32_t      e_flags;
               uint16_t      e_ehsize;
               uint16_t      e_phentsize;
               uint16_t      e_phnum;
               uint16_t      e_shentsize;
               uint16_t      e_shnum;
               uint16_t      e_shstrndx;
           } ElfN_Ehdr;

```

#### 2.2 ELF 程序头

> 段是在内核转载是被解析的，描述了磁盘上可执行文件的内存布局以及如何映射到内存中。可以通过引用原始ELF头中名为==e_phoff==程序头偏移量来得到程序头表。
>
> 接下来介绍5中常见的elf程序头类型



> 首先看下 Elf32_Phdr/Elf64_Phdr结构体

```c
		typedef struct {
               uint32_t   p_type;
               Elf32_Off  p_offset;
               Elf32_Addr p_vaddr;
               Elf32_Addr p_paddr;
               uint32_t   p_filesz;
               uint32_t   p_memsz;
               uint32_t   p_flags;
               uint32_t   p_align;
           } Elf32_Phdr;

           typedef struct {
               uint32_t   p_type;
               uint32_t   p_flags;
               Elf64_Off  p_offset;
               Elf64_Addr p_vaddr;
               Elf64_Addr p_paddr;
               uint64_t   p_filesz;
               uint64_t   p_memsz;
               uint64_t   p_align;
           } Elf64_Phdr;

```



##### 2.2.1 PT_LOAD

> 一个可执行文件至少有一个PT_LOAD类型的段。这类程序头描述的是可装载的段，也就是，这种类型的段将被装载或者映射到内存中。

> 一个需要动态链接的ELF可执行文件通常包含了以下两个可装载的段（类型为PT_LOAD）:
>
> - 存放程序代码的text段
> - 存放全局变量的动态链接信息的data段

##### 2.2.2 PT_DYNAMIC -- 动态段的Phdr

> 动态段是动态链接可执行文件所持有的，包含了动态链接器所必须的一些信息。在动态段中包含了一些标记值和指针。
>
> - 运行时需要链接的共享库列表
> - 全局偏离表的地址 ----ELF动态链接部分
> - 重定位条目的相关信息

##### 2.2.3 PT_NOTE

PT_NOTE 类型的段可能保存了与特定供应商或者系统相关的附加信息。

##### 2.2.4 PT_INTERP

PT_INTERP 段只将位置和大小信息存放在一个以NULL为终止符的字符串中，是对程序解释器位置的描述。 /lib/ld-linux.so.2。一般是指动态链接器的位置，也即程序解释器位置

##### 2.2.5 PT_PHDR

> 保存了程序头表本身的位置和大小。Phdr表保存了所有的Phdr对文件中段的描述信息。

![微信图片_20180722201625](/微信图片_20180722201625.png)

#### 2.3 ELF节头

##### 2.3.1 .text节

> 保存了程序代码指令的代码节

##### 2.3.2 .rodata节

> 保存了只读的数据

存放的数据例如：

==printf("hello world \n");==

##### 2.3.3 .plt节

过程链接表

##### 2.3.4 .data节

.data节 存在data段，保存了初始化的全局变量等数据。

##### 2.3.5 .bss节

保存了未进行初始化的全局变量，是data段的一部分，占用空间不超过4个字节，仅表示这个节的本身空间。

##### 2.3.6 .got.plt节

 .got保存了全局偏移表。.got和.plt节一起提供了对导入的共享库函数的访问入口，由动态链接器在运行时进行修改。

##### 2.3.7 .dynsym节

保存了从共享库导入的动态符号信息，该节保存在text段中

##### 2.3.8 .dynstr节

.dynstr节保存了动态符号字符串表，表中存放了一系列字符串，这些字符串代表了符号的名称，以空字符作为终止符。

##### 2.3.9 .rel.* 节

重定位保存了重定位相关的信息

##### 2.3.10 .hash 节

有时也称为 .gnu.bash，保存了一个用于查找符号的散列表。

##### 2.3.11 .symtab节

##### 2.3.12 .strtab节

保存了符号字符串表

##### 2.3.13 .shstrtab节

保存节头字符串表

##### 2.3.14 .ctors和 .dtors节

.ctors构造器 和 .dtors析构器。这两个姐保存了指向构造函数和析构函数的函数指针，构造函数是在main函数执行之前需要执行的代码，析构函数是在main函数之后需要执行的代码。

#### 2.4 ELF符号

```shell
e@ee:~/elf/test$ readelf -S 1 //查看文件的节头
There are 31 section headers, starting at offset 0x1a10:

Section Headers:
  [Nr] Name              Type             Address           Offset
  ### Flags 
  ### A（alloc） 表示有该标记的节会在运行时分配并装载进入内存 
  ### WA(write/alloc) 
  ### AX(alloc/exec)
       Size              EntSize          Flags  Link  Info  Align
  [ 0]                   NULL             0000000000000000  00000000
       0000000000000000  0000000000000000           0     0     0
  ####     
  [ 1] .interp           PROGBITS         0000000000400238  00000238
       000000000000001c  0000000000000000   A       0     0     1
  ####     
  [ 2] .note.ABI-tag     NOTE             0000000000400254  00000254
       0000000000000020  0000000000000000   A       0     0     4
  ####     
  [ 3] .note.gnu.build-i NOTE             0000000000400274  00000274
       0000000000000024  0000000000000000   A       0     0     4
  ####符号散列表 
  [ 4] .gnu.hash         GNU_HASH         0000000000400298  00000298
       000000000000001c  0000000000000000   A       5     0     8
  ####共享目标文件符号数据   **保存了引用来自外部文件符号的全局符号 对于动态链接可执行来说是必需的
  ### 
  [ 5] .dynsym           DYNSYM           00000000004002b8  000002b8
       0000000000000078  0000000000000018   A       6     1     8
  ####共享目标文件符号名称   
  [ 6] .dynstr           STRTAB           0000000000400330  00000330
       0000000000000046  0000000000000000   A       0     0     1
  [ 7] .gnu.version      VERSYM           0000000000400376  00000376
       000000000000000a  0000000000000002   A       5     0     2
  [ 8] .gnu.version_r    VERNEED          0000000000400380  00000380
       0000000000000020  0000000000000000   A       6     1     8
  [ 9] .rela.dyn         RELA             00000000004003a0  000003a0
       0000000000000018  0000000000000018   A       5     0     8
  [10] .rela.plt         RELA             00000000004003b8  000003b8
       0000000000000048  0000000000000018  AI       5    24     8
  [11] .init             PROGBITS         0000000000400400  00000400
       000000000000001a  0000000000000000  AX       0     0     4
  ####过程连接表
  [12] .plt              PROGBITS         0000000000400420  00000420
       0000000000000040  0000000000000010  AX       0     0     16
  [13] .plt.got          PROGBITS         0000000000400460  00000460
       0000000000000008  0000000000000000  AX       0     0     8
  ####程序代码     
  [14] .text             PROGBITS         0000000000400470  00000470
       00000000000001b2  0000000000000000  AX       0     0     16
  [15] .fini             PROGBITS         0000000000400624  00000624
       0000000000000009  0000000000000000  AX       0     0     4
  ####只读数据     
  [16] .rodata           PROGBITS         0000000000400630  00000630
       0000000000000015  0000000000000000   A       0     0     4
  [17] .eh_frame_hdr     PROGBITS         0000000000400648  00000648
       0000000000000034  0000000000000000   A       0     0     4
  [18] .eh_frame         PROGBITS         0000000000400680  00000680
       00000000000000f4  0000000000000000   A       0     0     8
  [19] .init_array       INIT_ARRAY       0000000000600e10  00000e10
       0000000000000008  0000000000000000  WA       0     0     8
  [20] .fini_array       FINI_ARRAY       0000000000600e18  00000e18
       0000000000000008  0000000000000000  WA       0     0     8
  [21] .jcr              PROGBITS         0000000000600e20  00000e20
       0000000000000008  0000000000000000  WA       0     0     8
  #### 动态链接结构和对象
  [22] .dynamic          DYNAMIC          0000000000600e28  00000e28
       00000000000001d0  0000000000000010  WA       6     0     8
  [23] .got              PROGBITS         0000000000600ff8  00000ff8
       0000000000000008  0000000000000008  WA       0     0     8
  #### 全局偏移表
  [24] .got.plt          PROGBITS         0000000000601000  00001000
       0000000000000030  0000000000000008  WA       0     0     8
  #### 全局的初始化变量
  [25] .data             PROGBITS         0000000000601030  00001030
       0000000000000010  0000000000000000  WA       0     0     8
  #### 全局未初始化的变量
  [26] .bss              NOBITS           0000000000601040  00001040
       0000000000000008  0000000000000000  WA       0     0     1
  [27] .comment          PROGBITS         0000000000000000  00001040
       0000000000000035  0000000000000001  MS       0     0     1
  [28] .shstrtab         STRTAB           0000000000000000  000018fe
       000000000000010c  0000000000000000           0     0     1
  #### dynsym保存的符号是symtab所保存的符号的子集，.symtab中还保存了可执行文件的本地符号，如全局变量，或本地函数等。其保存了所有符号，只是用来进行调试和链接的。有时候是可以删除掉的
  [29] .symtab           SYMTAB           0000000000000000  00001078
       0000000000000660  0000000000000018          30    47     8
  [30] .strtab           STRTAB           0000000000000000  000016d8
       0000000000000226  0000000000000000           0     0     1
Key to Flags:
  W (write), A (alloc), X (execute), M (merge), S (strings), l (large)
  I (info), L (link order), G (group), T (TLS), E (exclude), x (unknown)
  O (extra OS processing required) o (OS specific), p (processor specific)

```

上面是对节头的分析

下面分析一个elf文件符号项的结构

```c
typedef struct {
    uint32_t      st_name;
    Elf32_Addr    st_value;
    uint32_t      st_size;
    unsigned char st_info;
    unsigned char st_other;
    uint16_t      st_shndx;
} Elf32_Sym;

typedef struct {
    uint32_t      st_name;
    unsigned char st_info;
    unsigned char st_other;
    uint16_t      st_shndx;
    Elf64_Addr    st_value;
    uint64_t      st_size;
} Elf64_Sym;
//两个节头的项内存偏移量是不同的

```

2.5

2.6

2.7

第三章

第四章

第五章

第六章

第七章

第八章

第九章