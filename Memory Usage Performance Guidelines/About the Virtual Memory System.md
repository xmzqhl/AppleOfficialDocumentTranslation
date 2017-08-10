#About the Virtual Memory System
#关于虚拟内存系统
[原文](https://developer.apple.com/library/content/documentation/Performance/Conceptual/ManagingMemory/Articles/AboutMemory.html#//apple_ref/doc/uid/20001880-BCICIHAB)

Efficient memory management is an important aspect of writing high performance code in both OS X and iOS. Minimizing memory usage not only decreases your application’s memory footprint, it can also reduce the amount of CPU time it consumes. In order to properly tune your code though, you need to understand something about how the underlying system manages memory.

对OS X和iOS来说，高效的内存管理是写高性能代码的一个重要方面。最小化内存使用，不仅仅减少了你的应用程序的内存占用，同时也减少了它消耗的CPU时间。为了正确地调整代码，你需要理解底层系统是如何管理内存的。

Both OS X and iOS include a fully-integrated virtual memory system that you cannot turn off; it is always on. Both systems also provide up to 4 gigabytes of addressable space per 32-bit process. In addition, OS X provides approximately 18 exabytes of addressable space for 64-bit processes. Even for computers that have 4 or more gigabytes of RAM available, the system rarely dedicates this much RAM to a single process.

OS X和iOS都包含了一个你无法关闭的完全整合的虚拟内存系统；它总是开启的。两个系统都为每个32位进程提供了高达4GB的可寻址空间。另外，OS X为64位进程提供了18EB可寻址空间。即使对于有4GB或更多可用RAM的计算机，该系统也很少分配这么多RAM给单个进程。

To give processes access to their entire 4 gigabyte or 18 exabyte address space, OS X uses the hard disk to hold data that is not currently in use. As memory gets full, sections of memory that are not being used are written to disk to make room for data that is needed now. The portion of the disk that stores the unused data is known as the backing store because it provides the backup storage for main memory.

为了使进程访问他们的整个4GB或18EB地址空间，OS X使用硬盘来保存当前未使用的数据。当内存已满时，未使用的内存区域将写入磁盘，为现在需要使用的数据腾出空间。存储未使用数据的磁盘部分称为后备存储器。因为它为主存提供了备份存储。

Although OS X supports a backing store, iOS does not. In iPhone applications, read-only data that is already on the disk (such as code pages) is simply removed from memory and reloaded from disk as needed. Writable data is never removed from memory by the operating system. Instead, if the amount of free memory drops below a certain threshold, the system asks the running applications to free up memory voluntarily to make room for new data. Applications that fail to free up enough memory are terminated.

尽管OS X支持后备存储器，但iOS并不支持。在iPhone的应用中，已经在磁盘上的只读数据(例如代码段)从内存中移除和需要时从磁盘重新加载。可写数据永远不会被操作系统从内存中移除。相反，如果可用内存下降到某个特定的阈值，系统会要求正在运行的应用自动释放内存来为新的数据腾出空间。没有释放出足够内存的应用程序将被终止。

>Note: Unlike most UNIX-based operating systems, OS X does not use a preallocated disk partition for the backing store. Instead, it uses all of the available space on the machine’s boot partition.
>
>注意：与大多数基于UNIX的操作系统不同，OS X不会为备份存储使用一个预先分配的磁盘分区。而是使用机器上引导分区的所有可用空间。

The following sections introduce terminology and provide a brief overview of the virtual memory system used in both OS X and iOS. For more detailed information on how the virtual memory system works, see [Kernel Programming Guide](https://developer.apple.com/library/content/documentation/Darwin/Conceptual/KernelProgramming/About/About.html#//apple_ref/doc/uid/TP30000905).

以下部分介绍在OS X和iOS中的虚拟内存系统的术语和简要概述。 要获得虚拟内存系统如何工作的详细信息，请参阅[内核编程指南](https://developer.apple.com/library/content/documentation/Darwin/Conceptual/KernelProgramming/About/About.html#//apple_ref/doc/uid/TP30000905)

##About Virtual Memory  关于虚拟内存
Virtual memory allows an operating system to escape the limitations of physical RAM. The virtual memory manager creates a logical address space (or “virtual” address space) for each process and divides it up into uniformly-sized chunks of memory called pages. The processor and its memory management unit (MMU) maintain a page table to map pages in the program’s logical address space to hardware addresses in the computer’s RAM. When a program’s code accesses an address in memory, the MMU uses the page table to translate the specified logical address into the actual hardware memory address. This translation occurs automatically and is transparent to the running application.

虚拟内存允许操作系统摆脱物理RAM的限制。虚拟内存管理器为每个进程创建一个逻辑地址空间(或“虚拟”地址空间)，并把它分割为统一大小的内存块，成为页。处理器和他的内存管理单元(MMU)维护一个页表(page table)来把程序的逻辑地址空间的页映射到计算机RAM的硬件地址。当一个程序的代码访问内存的一个地址时，MMU使用这个页表来翻译指定的逻辑地址到实际的硬件内存地址。这种翻译是自动发生的，对运行的程序来说是透明的。

As far as a program is concerned, addresses in its logical address space are always available. However, if an application accesses an address on a memory page that is not currently in physical RAM, a page fault occurs. When that happens, the virtual memory system invokes a special page-fault handler to respond to the fault immediately. The page-fault handler stops the currently executing code, locates a free page of physical memory, loads the page containing the needed data from disk, updates the page table, and then returns control to the program’s code, which can then access the memory address normally. This process is known as paging.

就程序关心的而言，逻辑地址空间的地址总是可用的。但是，如果一个应用程序访问当前不在物理RAM中的一个内存页上的地址，则会发生页面错误。当这种情况发生时，虚拟内存系统调用一个特定的页面故障处理程序来立即响应故障。页面故障处理程序停止当前执行的代码，找到一个物理内存的空闲页面，将需要从磁盘加载的数据加载到这个页，更新页表，然后返回控制权给程序代码，从而使程序代码可以正常的访问内存地址。这个过程称为分页(paging)。

If there are no free pages available in physical memory, the handler must first release an existing page to make room for the new page. How the system release pages depends on the platform. In OS X, the virtual memory system often writes pages to the backing store. The backing store is a disk-based repository containing a copy of the memory pages used by a given process. Moving data from physical memory to the backing store is called paging out (or “swapping out”); moving data from the backing store back in to physical memory is called paging in (or “swapping in”). In iOS, there is no backing store and so pages are are never paged out to disk, but read-only pages are still be paged in from disk as needed

如果在物理内存中没有可用的空间页，处理程序必须先释放一个已存在的页来给新数据腾出空间。系统如何释放页取决于系统平台。在OS X中，虚拟内存系统经常把页写到备份存储。备份存储是一个基于磁盘的存储仓库，其中包含给定进程使用的内存页的副本。把数据从物理内存移动到备份存储叫做页出(或者“换出”)；从备份存储把数据移动到物理内存叫做页入(或“换入”)。在iOS中，没有备份存储，所以页从来不会交换到磁盘中去，但是只读页仍然可以在需要时从磁盘“页入”。

In OS X and in earlier versions of iOS, the size of a page is 4 kilobytes. In later versions of iOS, A7- and A8-based systems expose 16-kilobyte pages to the 64-bit userspace backed by 4-kilobyte physical pages, while A9 systems expose 16-kilobyte pages backed by 16-kilobyte physical pages. These sizes determine how many kilobytes the system reads from disk when a page fault occurs. Disk thrashing can occur when the system spends a disproportionate amount of time handling page faults and reading and writing pages, rather than executing code for a program.

在OS X和早期的iOS版本中，页大小是4KB。在iOS更高的版本中，基于A7和A8的系统，暴露16KB页给被4KB物理页所支持的64位用户空间。而A9系统暴露了被16KB物理页所支持16KB页。这些大小决定了当发生页面错误时，系统从磁盘读取多少KB的字节。当系统花费不成比例的时间处理页面错误和读写页，而不是为程序执行代码时，可能发生磁盘震颤(disk thrashing)。

Paging of any kind, and disk thrashing in particular, affects performance negatively because it forces the system to spend a lot of time reading and writing to disk. Reading a page in from the backing store takes a significant amount of time and is much slower than reading directly from RAM. If the system has to write a page to disk before it can read another page from disk, the performance impact is even worse.

任何类型的分页，特别是磁盘震颤，会对性能产生负面影响，因为它会迫使系统花费大量时间来读写硬盘。从备份存储中读取一个页需要大量的时间，比直接从RAM读取的速度慢得多。如果系统必须在它可以从磁盘读取另一个页之前向磁盘写一个页，则性能影响甚至更糟。
##Details of the Virtual Memory System 虚拟内存系统的细节
The logical address space of a process consists of mapped regions of memory. Each mapped memory region contains a known number of virtual memory pages. Each region has specific attributes controlling such things as inheritance (portions of the region may be mapped from “parent” regions), write-protection, and whether it is wired (that is, it cannot be paged out). Because regions contain a known number of pages, they are page-aligned, meaning the starting address of the region is also the starting address of a page and the ending address also defines the end of a page.









