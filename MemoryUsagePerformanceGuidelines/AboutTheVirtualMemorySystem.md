# About the Virtual Memory System 关于虚拟内存系统
[原文](https://developer.apple.com/library/content/documentation/Performance/Conceptual/ManagingMemory/Articles/AboutMemory.html#//apple_ref/doc/uid/20001880-BCICIHAB)

Efficient memory management is an important aspect of writing high performance code in both OS X and iOS. Minimizing memory usage not only decreases your application’s memory footprint, it can also reduce the amount of CPU time it consumes. In order to properly tune your code though, you need to understand something about how the underlying system manages memory.

对OS X和iOS来说，高效的内存管理是写高性能代码的一个重要方面。最小化内存使用，不仅仅减少了你的应用程序的内存占用，同时也减少了它消耗的CPU时间。为了正确地调整代码，你需要理解底层系统是如何管理内存的。

Both OS X and iOS include a fully-integrated virtual memory system that you cannot turn off; it is always on. Both systems also provide up to 4 gigabytes of addressable space per 32-bit process. In addition, OS X provides approximately 18 exabytes of addressable space for 64-bit processes. Even for computers that have 4 or more gigabytes of RAM available, the system rarely dedicates this much RAM to a single process.

OS X和iOS都包含了一个你无法关闭的完全整合的虚拟内存系统；它总是开启的。两个系统都为每个32位进程提供了高达4GB的可寻址空间。另外，OS X为64位进程提供了18EB可寻址空间。即使对于有4GB或更多可用RAM的计算机，该系统也很少分配这么多RAM给单个进程。

To give processes access to their entire 4 gigabyte or 18 exabyte address space, OS X uses the hard disk to hold data that is not currently in use. As memory gets full, sections of memory that are not being used are written to disk to make room for data that is needed now. The portion of the disk that stores the unused data is known as the backing store because it provides the backup storage for main memory.

为了使进程访问他们的整个4GB或18EB地址空间，OS X使用硬盘来保存当前未使用的数据。当内存已满时，未使用的内存区域将写入磁盘，为现在需要使用的数据腾出空间。存储未使用数据的磁盘部分称为**后备存储器(backing store)**。因为它为主存提供了备份存储。

Although OS X supports a backing store, iOS does not. In iPhone applications, read-only data that is already on the disk (such as code pages) is simply removed from memory and reloaded from disk as needed. Writable data is never removed from memory by the operating system. Instead, if the amount of free memory drops below a certain threshold, the system asks the running applications to free up memory voluntarily to make room for new data. Applications that fail to free up enough memory are terminated.

尽管OS X支持后备存储器，但iOS并不支持。在iPhone的应用中，已经在磁盘上的只读数据(例如代码段)从内存中移除和需要时从磁盘重新加载。可写数据永远不会被操作系统从内存中移除。相反，如果可用内存下降到某个特定的阈值，系统会要求正在运行的应用自动释放内存来为新的数据腾出空间。没有释放出足够内存的应用程序将被终止。

>Note: Unlike most UNIX-based operating systems, OS X does not use a preallocated disk partition for the backing store. Instead, it uses all of the available space on the machine’s boot partition.
>
>注意：与大多数基于UNIX的操作系统不同，OS X不会为后备存储器使用一个预先分配的磁盘分区。而是使用机器上引导分区的所有可用空间。

The following sections introduce terminology and provide a brief overview of the virtual memory system used in both OS X and iOS. For more detailed information on how the virtual memory system works, see [Kernel Programming Guide](https://developer.apple.com/library/content/documentation/Darwin/Conceptual/KernelProgramming/About/About.html#//apple_ref/doc/uid/TP30000905).

以下部分介绍在OS X和iOS中的虚拟内存系统的术语和简要概述。 要获得虚拟内存系统如何工作的详细信息，请参阅[内核编程指南](https://developer.apple.com/library/content/documentation/Darwin/Conceptual/KernelProgramming/About/About.html#//apple_ref/doc/uid/TP30000905)

## About Virtual Memory  关于虚拟内存
Virtual memory allows an operating system to escape the limitations of physical RAM. The virtual memory manager creates a logical address space (or “virtual” address space) for each process and divides it up into uniformly-sized chunks of memory called pages. The processor and its memory management unit (MMU) maintain a page table to map pages in the program’s logical address space to hardware addresses in the computer’s RAM. When a program’s code accesses an address in memory, the MMU uses the page table to translate the specified logical address into the actual hardware memory address. This translation occurs automatically and is transparent to the running application.

虚拟内存允许操作系统摆脱物理RAM的限制。虚拟内存管理器为每个进程创建一个逻辑地址空间(或“虚拟”地址空间)，并把它分割为统一大小的内存块，成为页。处理器和他的内存管理单元(MMU)维护一个页表(page table)来把程序的逻辑地址空间的页映射到计算机RAM的硬件地址。当一个程序的代码访问内存的一个地址时，MMU使用这个页表来翻译指定的逻辑地址到实际的硬件内存地址。这种翻译是自动发生的，对运行的程序来说是透明的。

As far as a program is concerned, addresses in its logical address space are always available. However, if an application accesses an address on a memory page that is not currently in physical RAM, a page fault occurs. When that happens, the virtual memory system invokes a special page-fault handler to respond to the fault immediately. The page-fault handler stops the currently executing code, locates a free page of physical memory, loads the page containing the needed data from disk, updates the page table, and then returns control to the program’s code, which can then access the memory address normally. This process is known as paging.

就程序关心的而言，逻辑地址空间的地址总是可用的。但是，如果一个应用程序访问当前不在物理RAM中的一个内存页上的地址，则会发生页面错误。当这种情况发生时，虚拟内存系统调用一个特定的页面故障处理程序来立即响应故障。页面故障处理程序停止当前执行的代码，找到一个物理内存的空闲页面，将需要从磁盘加载的数据加载到这个页，更新页表，然后返回控制权给程序代码，从而使程序代码可以正常的访问内存地址。这个过程称为分页(paging)。

If there are no free pages available in physical memory, the handler must first release an existing page to make room for the new page. How the system release pages depends on the platform. In OS X, the virtual memory system often writes pages to the backing store. The backing store is a disk-based repository containing a copy of the memory pages used by a given process. Moving data from physical memory to the backing store is called paging out (or “swapping out”); moving data from the backing store back in to physical memory is called paging in (or “swapping in”). In iOS, there is no backing store and so pages are are never paged out to disk, but read-only pages are still be paged in from disk as needed

如果在物理内存中没有可用的空间页，处理程序必须先释放一个已存在的页来给新数据腾出空间。系统如何释放页取决于系统平台。在OS X中，虚拟内存系统经常把页写到后备存储器。后备存储器是一个基于磁盘的存储仓库，其中包含给定进程使用的内存页的副本。把数据从物理内存移动到后备存储器叫做页出(或者“换出”)；从后备存储器把数据移动到物理内存叫做页入(或“换入”)。在iOS中，没有后备存储器，所以页从来不会交换到磁盘中去，但是只读页仍然可以在需要时从磁盘“页入”。

In OS X and in earlier versions of iOS, the size of a page is 4 kilobytes. In later versions of iOS, A7- and A8-based systems expose 16-kilobyte pages to the 64-bit userspace backed by 4-kilobyte physical pages, while A9 systems expose 16-kilobyte pages backed by 16-kilobyte physical pages. These sizes determine how many kilobytes the system reads from disk when a page fault occurs. Disk thrashing can occur when the system spends a disproportionate amount of time handling page faults and reading and writing pages, rather than executing code for a program.

在OS X和早期的iOS版本中，页大小是4KB。在iOS更高的版本中，基于A7和A8的系统，暴露16KB页给被4KB物理页所支持的64位用户空间。而A9系统暴露了被16KB物理页所支持16KB页。这些大小决定了当发生页面错误时，系统从磁盘读取多少KB的字节。当系统花费不成比例的时间处理页面错误和读写页，而不是为程序执行代码时，可能发生磁盘震颤(disk thrashing)。

Paging of any kind, and disk thrashing in particular, affects performance negatively because it forces the system to spend a lot of time reading and writing to disk. Reading a page in from the backing store takes a significant amount of time and is much slower than reading directly from RAM. If the system has to write a page to disk before it can read another page from disk, the performance impact is even worse.

任何类型的分页，特别是磁盘震颤，会对性能产生负面影响，因为它会迫使系统花费大量时间来读写硬盘。从后备存储器中读取一个页需要大量的时间，比直接从RAM读取的速度慢得多。如果系统必须在它可以从磁盘读取另一个页之前向磁盘写一个页，则性能影响甚至更糟。
## Details of the Virtual Memory System 虚拟内存系统的细节
The logical address space of a process consists of mapped regions of memory. Each mapped memory region contains a known number of virtual memory pages. Each region has specific attributes controlling such things as inheritance (portions of the region may be mapped from “parent” regions), write-protection, and whether it is wired (that is, it cannot be paged out). Because regions contain a known number of pages, they are page-aligned, meaning the starting address of the region is also the starting address of a page and the ending address also defines the end of a page.

进程的逻辑地址空间由映射的内存区域组成。每个映射的内存区域包含已知数量的虚拟内存页。每个区域具有特定的属性，控制诸如继承(区域的部分可能从“父”区域映射)，写保护，和它是否被连线(即，它不能被页出[page out])。因为区域包含已知数量的页面，他们是页面对齐的，意味着区域的起始地址也是一个页面的起始地址，并且结束地址也定义了一个页面的结束地址。

The kernel associates a VM object with each region of the logical address space. The kernel uses VM objects to track and manage the resident and nonresident pages of the associated regions. A region can map to part of the backing store or to a memory-mapped file in the file system. Each VM object contains a map that associates regions with either the default pager or the vnode pager. The default pager is a system manager that manages the nonresident virtual memory pages in the backing store and fetches those pages when requested. The vnode pager implements memory-mapped file access. The vnode pager uses the paging mechanism to provide a window directly into a file. This mechanism lets you read and write portions of the file as if they were located in memory.

内核将一个VM对象和逻辑地址空间的每个区域相关联。内核使用VM对象追踪和管理与它关联区域的驻留和非驻留页。一个区域可以映射到后备存储器的一部分或者在文件系统中的一个内存映射文件。每个VM对象都包含一个映射， 它将区域与**默认分页器(default pager)**或**vnode分页器(vnode pager)**相关联。默认分页器是一个系统管理者， 用于管理在后备存储器中的非驻留虚拟内存页，并在请求时提取那些页面。vnode分页器实现内存映射文件访问。vnode分页器使用分页机制提供直接面向文件的窗口。这种机制可以让您读取和写入文件的一部分，就像它们在内存中一样。

In addition to mapping regions to either the default or vnode pager, a VM object may also map regions to another VM object. The kernel uses this self referencing technique to implement copy-on-write regions. Copy-on-write regions allow different processes (or multiple blocks of code within a process) to share a page as long as none of them write to that page. When a process attempts to write to the page, a copy of the page is created in the logical address space of the process doing the writing. From that point forward, the writing process maintains its own separate copy of the page, which it can write to at any time. Copy-on-write regions let the system share large quantities of data efficiently in memory while still letting processes manipulate those pages directly (and safely) if needed. These types of regions are most commonly used for the data pages loaded from system frameworks.

除了将区域映射到默认或vnode分页器之外，一个VM对象还可能将区域映射到另一个VM对象。内核使用这种自引用技术来实现**写时复制区域(copy-on-write regions)**。写时复制区域允许不同的进程(或一个进程的多个代码块)在不进行写入到页面的前提下来共享一个页面。当一个进程尝试写入到页面时，将在进行写入进程的逻辑地址空间中创建页面的副本。从那之后，写入进程维护自己单独的页面副本，他可以随时写入。写时复制区域让系统在内存中高效的共享大量数据，同时仍然允许进程直接(和安全地)操作那些页面。这些区域的类型最常用于从系统框架加载的数据页。

Each VM object contains several fields, as shown in Table 1.

每个VM对象包含几个字段，如表1所示。

**Table 1**  Fields of the VM object

**表1** VM对象的字段

Field 字段  |Description 描述 |
:---|:--------------------------|
 Resident pages|A list of the pages of this region that are currently resident in physical memory.  当前驻留在物理内存中的该区域的页面列表。 |
 Size | The size of the region, in bytes. 区域的大小,以字节为单位|
 Pager|The pager responsible for tracking and handling the pages of this region in backing store.负责追踪和处理在后备存储器中这个区域的页的分页器|
 Shadow|Used for copy-on-write optimizations.用于写时复制优化|
 Copy|Used for copy-on-write optimizations.用于写时复制优化|
 Attributes|Flags indicating the state of various implementation details.表示各种实现细节状态的标记|
 
 If the VM object is involved in a copy-on-write (vm_copy) operation, the shadow and copy fields may point to other VM objects. Otherwise both fields are usually NULL.

如果VM对象涉及到写时复制(vm_copy)操作，则shadow和copy字段可能指向其它VM对象。否则，两个字段通常为NULL。

## Wired Memory 联动内存
Wired memory (also called resident memory) stores kernel code and data structures that must never be paged out to disk. Applications, frameworks, and other user-level software cannot allocate wired memory. However, they can affect how much wired memory exists at any time. For example, an application that creates threads and ports implicitly allocates wired memory for the required kernel resources that are associated with them.

联动内存(也叫做常驻内存)存储内核代码和不能被页出到磁盘的数据结构。应用程序，框架，和其他用户级别的软件不能开辟联动内存。但是，它们可以随时影响联动内存存在的数量。例如，一个应用程序如果创建线程和端口的话，那么会隐式地为与其关联的它所需的内核资源分配联动内存。

Table 2 lists some of the wired-memory costs for application-generated entities.

表2列出了应用程序生成的实体的一些联动内存成本。

**Table 2**  Wired memory generated by user-level software

**表2** 通过用户级别软件生成的联动内存

|Resource(资源)|Wired Memory Used by Kernel(被内核使用的联动内存)|
|:-------------|:------------------|
|Process(进程)|16 kilobytes(16KB)|
| Thread(线程) |blocked in a continuation—5 kilobytes; blocked—21 kilobytes(在封闭的连续5KB;封闭的21KB)|
|Mach port(mach端口)|116 bytes(116字节)|
| Mapping(映射) |32 bytes(32字节)|
| Library(库) |2 kilobytes plus 200 bytes for each task that uses it(2KB加上为每个使用它的任务的200字节)|
|Memory region(内存区域)|160 bytes(160字节)|

> Note: These measurements may change with each new release of the operating system. They are provided here to give you a rough estimate of the relative cost of system resource usage.
> 
> 注意：这些测量值可能随着操作系统的每个新版本的发布而改变。这里提供了对系统资源使用的相对成本的粗略估计。

As you can see, every thread, process, and library contributes to the resident footprint of the system. In addition to your application using wired memory, however, the kernel itself requires wired memory for the following entities:

正如你所看到的,每个线程，进程，和库 都有助于系统的驻留足迹。除了你的使用联动内存的应用程序之外，内核本身为以下实体还需要联动内存：

* VM objects(VM对象)
* the virtual memory buffer cache(虚拟内存缓冲区缓存)
* I/O buffer caches(I/O缓冲区缓存)
* drivers (驱动)

Wired data structures are also associated with the physical page and map tables used to store virtual-memory mapping information, Both of these entities scale with the amount of available physical memory. Consequently, when you add memory to a system, the amount of wired memory increases even if nothing else changes. When a computer is first booted into the Finder, with no other applications running, wired memory can consume approximately 14 megabytes of a 64 megabyte system and 17 megabytes of a 128 megabyte system.

联动数据结构还与物理页和用于存储虚拟内存映射信息的映射表相关联。这两个实体都与可用物理内存的数量成比例。因此，当您向一个系统添加内存时，即使没有其他更改，联动内存也会增加。当一个计算机首次启动到Finder时，没有其它应用程序运行，联动内存在64MB系统中可以大约消耗14MB 和 在128MB系统中大约消耗17MB。

Wired memory pages are not immediately moved back to the free list when they become invalid. Instead they are “garbage collected” when the free-page count falls below the threshold that triggers page out events.

联动内存页并不是在它们变为无效时立即返回到空闲列表。相反，当空闲页的数量低于触发页出事件的阈值时，它们是垃圾回收的。
## Page Lists in the Kernel内核中的页面列表
The kernel maintains and queries three system-wide lists of physical memory pages:

内核维护和查询物理内存页的三个系统范围的列表：

* The active list contains pages that are currently mapped into memory and have been recently accessed.

    活动的列表包含当前映射到内存并最近被访问过的页面。

* The inactive list contains pages that are currently resident in physical memory but have not been accessed recently. These pages contain valid data but may be removed from memory at any time.

   非活动列表包含当前驻留在物理内存但最近未被访问过的页面。这些页面包含有效数据，但可能随时从内存中删除。

* The free list contains pages of physical memory that are not associated with any address space of VM object. These pages are available for immediate use by any process that needs them.

   空闲列表包含与VM对象的任何地址空间不相关联的物理内存页面。这些页面可供任何需要他们的进程立即使用。
   
When the number of pages on the free list falls below a threshold (determined by the size of physical memory), the pager attempts to balance the queues. It does this by pulling pages from the inactive list. If a page has been accessed recently, it is reactivated and placed on the end of the active list. In OS X, if an inactive page contains data that has not been written to the backing store recently, its contents must be paged out to disk before it can be placed on the free list. (In iOS, modified but inactive pages must remain in memory and be cleaned up by the application that owns them.) If an inactive page has not been modified and is not permanently resident (wired), it is stolen (any current virtual mappings to it are destroyed) and added to the free list. Once the free list size exceeds the target threshold, the pager rests.

当空闲列表页的数量降低到一定的阈值(由物理内存的大小决定)，**分页器(pager)**试图平衡队列。它通过从非活动列表拉取页面来实现。如果一个页面最近被访问了，它将被重新激活并放在活动列表的末尾。在OS X中，如果一个非活动页面包含最近尚未写入到后备存储器的数据，则它的内容必须在被放到空闲列表之前页出到磁盘。(在iOS中，被修改但非活动的页面必须保留在内存中，并由拥有它们的应用程序进行清理。)如果一个非活动页面还没有被修改，并且不是永久驻留(联动)，则它将被偷取(任何当前的虚拟映射都将被销毁)并被添加到空闲列表。一旦空闲列表的大小超过目标阈值， 分页器就会休眠。

The kernel moves pages from the active list to the inactive list if they are not accessed; it moves pages from the inactive list to the active list on a soft fault (see [Paging In Process](https://developer.apple.com/library/content/documentation/Performance/Conceptual/ManagingMemory/Articles/AboutMemory.html#//apple_ref/doc/uid/20001880-99598)). When virtual pages are swapped out, the associated physical pages are placed in the free list. Also, when processes explicitly free memory, the kernel moves the affected pages to the free list.

如果页面不被访问，内核将它们从活动列表移动到非活动列表；它将页面从非活动列表移动到一个软故障上的活动列表(参阅[页入处理](#paginginprocess))。当虚拟页面被换出时，相关联的物理页面被放到空闲列表中。此外，当进程显式释放内存时，内核将受影响的页面移动到空闲列表。
## Paging Out Process 页出处理
In OS X, when the number of pages in the free list dips below a computed threshold, the kernel reclaims physical pages for the free list by swapping inactive pages out of memory. To do this, the kernel iterates all resident pages in the active and inactive lists, performing the following steps:

在OS X中，当空闲列表的页数低于计算的一个阈值的时候，内核通过将非活动页面交换出内存来回收空闲列表的物理页。为此，内核迭代所有在活动和非活动列表中的常驻页面，执行以下步骤:

1. If a page in the active list is not recently touched, it is moved to the inactive list.

 	如果一个活动列表中的页面最近未被访问，它将被移动到非活动列表
 	
2. If a page in the inactive list is not recently touched, the kernel finds the page’s VM object.

	如果在非活动列表中的一个页面最近没有被访问，内核将找到该页面的VM对象。
 
3. If the VM object has never been paged before, the kernel calls an initialization routine that creates and assigns a default pager object.

	如果该VM对象之前从未被分页，内核将调用一个初始化例程来创建和指定一个默认分页器对象。

4. The VM object’s default pager attempts to write the page out to the backing store.

	该VM对象的默认分页器尝试将该页写出到后备存储器。

5. If the pager succeeds, the kernel frees the physical memory occupied by the page and moves the page from the inactive to the free list.

	如果该分页器成功了，内核释放该页面已占用的物理内存，并将该页从非活动列表移动到空闲列表。

> Note: In iOS, the kernel does not write pages out to a backing store. When the amount of free memory dips below the computed threshold, the kernel flushes pages that are inactive and unmodified and may also ask the running application to free up memory directly. For more information on responding to these notifications, see [Responding to Low-Memory Warnings in iOS](https://developer.apple.com/library/content/documentation/Performance/Conceptual/ManagingMemory/Articles/MemoryAlloc.html#//apple_ref/doc/uid/20001881-SW1).
> 
> 注意：在iOS中，内核不会将页面写出到后备存储器。当可用内存下降到计算出的阈值以下时，内核将刷新非活动和未修改的页面，并且还可能直接要求正在运行的应用程序来释放内存。有关响应这些通知的更多信息，请参阅[在iOS中响应低内存警告](TipsForAllocatingMemory.md#RtlmwiniOS)

## <a name="paginginprocess"></a>Paging In Process 页入处理
The final phase of virtual memory management moves pages into physical memory, either from the backing store or from the file containing the page data. A memory access fault initiates the page-in process. A memory access fault occurs when code tries to access data at a virtual address that is not mapped to physical memory. There are two kinds of faults:

虚拟内存管理的最终阶段将页面从后备存储器或从包含页面数据的文件中移动到物理内存中。内存访问故障启动页入程序。当代码尝试访问未映射到物理内存的虚拟地址上的数据时，会发生内存访问错误。有两种此类错误：

* A soft fault occurs when the page of the referenced address is resident in physical memory but is currently not mapped into the address space of this process.
	
	当引用地址的页面驻留在物理内存中，但当前并未映射到此进程的地址空间中时，会发生**软故障(soft fault)**。
	
* A hard fault occurs when the page of the referenced address is not in physical memory but is swapped out to backing store (or is available from a mapped file). This is what is typically known as a page fault.

	当引用地址的页面不在物理内存中但是被换出到后备存储器(或可从一个映射文件中获得)时，会发生**硬故障(hard fault)**。这通常被称为一个页面错误。

When any type of fault occurs, the kernel locates the map entry and VM object for the accessed region. The kernel then goes through the VM object’s list of resident pages. If the desired page is in the list of resident pages, the kernel generates a soft fault. If the page is not in the list of resident pages, it generates a hard fault.

当发生任何类型的故障时，内核定位映射条目和访问区域的VM对象。然后，内核遍历VM对象的常驻页面列表。如果所需的页面在常驻页面列表中，则内核会产生软故障。如果页面不在常驻页面列表中，则会生成一个硬故障。

For soft faults, the kernel maps the physical memory containing the pages to the virtual address space of the process. The kernel then marks the specific page as active. If the fault involved a write operation, the page is also marked as modified so that it will be written to backing store if it needs to be freed later.

对于软故障，内核将包含页面的物理内存映射到进程的虚拟地址空间。然后，内核将这个特定的页面标记为活动的。如果这个故障涉及写入操作，则页面也被标记为已修改，以便如果稍后它需要被释放，它将被写入到后备存储器。

For hard faults, the VM object’s pager finds the page in the backing store or from the file on disk, depending on the type of pager. After making the appropriate adjustments to the map information, the pager moves the page into physical memory and places the page on the active list. As with a soft fault, if the fault involved a write operation, the page is marked as modified.

对于硬故障来说，根据页面的类型的不同，VM对象的分页器在后备存储器或磁盘上的文件找到该页面。在对映射信息进行适当的调整后，分页器将页面移动到物理内存中，并将页面放在活动列表上。与软故障一样，如果该故障涉及写入操作，页面将被标记为已修改。