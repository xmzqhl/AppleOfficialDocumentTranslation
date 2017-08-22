# Viewing Virtual Memory Usage 查看虚拟内存使用
If you need more detailed information about virtual memory usage, you can use the `top`, `vm_stat`, `pagestuff`, and `vmmap` command-line tools for analyzing your Mac apps. The information returned by these tools ranges from summary information about all the system processes to detailed information about a specific process.

如果您需要有关虚拟内存使用的详细信息，您可以使用`top`，`vm_stat`，`pagestuff`，和`vmmap`命令行工具来分析您的Mac应用程序。这些工具返回的信息从有关所有系统进程的摘要信息到一个特定进程的详细信息排列。

The following sections provide information on using the `vm_stat`, `pagestuff`, and `vmmap` tools to gather detailed memory information. For more information on using Instruments to analyze memory, see [Instruments User Guide](https://developer.apple.com/library/content/documentation/DeveloperTools/Conceptual/InstrumentsUserGuide/index.html#//apple_ref/doc/uid/TP40004652) and the other articles in this document. For information on how to use the `top` tool, see [Performance Overview](https://developer.apple.com/library/content/documentation/Performance/Conceptual/PerformanceOverview/Introduction/Introduction.html#//apple_ref/doc/uid/TP40001410).

以下部分提供使用`vm_stat`，`pagestuff`以及`vmmap`工具来收集内存详细信息方面的信息。有关使用Instruments分析内存的更多信息，请参阅[Instruments用户指南](https://developer.apple.com/library/content/documentation/DeveloperTools/Conceptual/InstrumentsUserGuide/index.html#//apple_ref/doc/uid/TP40004652)和本文档中的其他文章。有关如何使用该`top`工具的信息，请参阅[性能概述](https://developer.apple.com/library/content/documentation/Performance/Conceptual/PerformanceOverview/Introduction/Introduction.html#//apple_ref/doc/uid/TP40001410)。

## Viewing Virtual Memory Statistics 查看虚拟内存统计信息
The `vm_stat` tool displays high-level statistics about the current virtual memory usage of the system. By default, `vm_stat` displays these statistics once, but you can specify an interval value (in seconds) to update these statistics continuously. For information on the usage of this tool, see the `vm_stat` man page.

该`vm_stat`工具显示关于系统当前虚拟内存使用情况的高级统计信息。默认情况下，`vm_stat`显示这些统计一次，但您可以指定一个间隔值（以秒为单位），以持续更新这些统计信息。有关此工具的使用的信息，请参阅`vm_stat`手册页。

Listing 1 shows an example of the output from `vm_stat`.

清单1显示了从`vm_stat`输出的一个示例。

**Listing 1**  Output of vm_stat tool

**清单1** vm_stat工具的输出

```
Mach Virtual Memory Statistics: (page size of 4096 bytes)
Pages free:                     3194.
Pages active:                  34594.
Pages inactive:                17870.
Pages wired down:               9878.
"Translation faults":        6333197.
Pages copy-on-write:           81385.
Pages zero filled:           3180051.
Pages reactivated:            343961.
Pageins:                       33043.
Pageouts:                      78496.
Object cache: 66227 hits of 96952 lookups (68% hit rate)
```
## Viewing Mach-O Code Pages 查看Mach-O代码页
The `pagestuff` tool displays information about the specified logical pages of a file conforming to the Mach-O executable format. For each specified page of code, symbols (function and static data structure names) are displayed. All pages in the `__TEXT, __text` section are displayed if no page numbers are given.

该`pagestuff`工具显示有关符合Mach-O可执行格式文件的指定逻辑页面的信息。对于每个指定的代码页，符号（函数和静态数据结构名称）被显示出来。如果没有给出页码，所有在`__TEXT, __text`区域(section)的页面将会被显示。

Listing 2 shows part of the output from `pagestuff `for the TextEdit application. This output is the result of running the tool with the `-a` option, which prints information about all of the executable’s code pages. It includes the virtual address locations of each page and the type of information on that page.

清单2显示了TextEdit应用程序的`pagestuff`的输出的一部分。该输出是使用`-a`选项运行该工具的结果，该选项打印所有可执行代码页的信息。它包括每个页面的虚拟地址位置和该页面上的信息类型。

**Listing 2**  Partial output of pagestuff tool

**清单2** pagestuff工具的部分输出

```
File Page 0 contains Mach-O headers
File Page 1 contains Mach-O headers
File Page 2 contains contents of section (__TEXT,__text)
Symbols on file page 2 virtual address 0x3a08 to 0x4000
File Page 3 contains contents of section (__TEXT,__text)
Symbols on file page 3 virtual address 0x4000 to 0x5000
File Page 4 contains contents of section (__TEXT,__text)
Symbols on file page 4 virtual address 0x5000 to 0x6000
 
...
 
File Page 22 contains contents of section (__TEXT,__cstring)
File Page 22 contains contents of section (__TEXT,__literal4)
File Page 22 contains contents of section (__TEXT,__literal8)
File Page 22 contains contents of section (__TEXT,__const)
Symbols on file page 22 virtual address 0x17000 to 0x17ffc
File Page 23 contains contents of section (__DATA,__data)
File Page 23 contains contents of section (__DATA,__la_symbol_ptr)
File Page 23 contains contents of section (__DATA,__nl_symbol_ptr)
File Page 23 contains contents of section (__DATA,__dyld)
File Page 23 contains contents of section (__DATA,__cfstring)
File Page 23 contains contents of section (__DATA,__bss)
File Page 23 contains contents of section (__DATA,__common)
Symbols on file page 23 virtual address 0x18000 to 0x18d48
 0x00018000 _NXArgc
 0x00018004 _NXArgv
 0x00018008 _environ
 0x0001800c ___progname
...
```
In the preceding listing, if a page exports any symbols, those symbols are also displayed by the `-a` option. If you want to view the symbols for a single page, pass in the desired page number instead of the `-a` option. For more information about the `pagestuff` tool and its supported options, see the `pagestuff` man page.

在上面的列表中，如果一个页面输出任何符号，则这些符号也可以通过该`-a`选项显示。如果您要查看单个页面的符号，请传递所需的页码来替代`-a`选项。有关该`pagestuff`工具及其支持的选项的更多信息，请参阅`pagestuff`手册页。

## Viewing Virtual Memory Regions 查看虚拟内存区域
The `vmmap` and `vmmap64` tools display the virtual memory regions allocated for a specified process. These tools provide access to the virtual memory of 32-bit and 64-bit applications, respectively. You can use them to understand the purpose of memory at a given address and how that memory is being used. For each virtual-memory region, these tools display the type of page, the starting address, region size (in kilobytes), read/write permissions, sharing mode, and the purpose of the pages in that region.

`vmmap`和`vmmap64`工具显示分配给一个特定进程的虚拟内存区域。这些工具分别提供对32位和64位应用程序的虚拟内存的访问。您可以使用它们来了解一个给定地址上的内存的用途以及该内存如何被使用。对于每个虚拟内存区域，这些工具显示页面的类型，起始地址，区域大小（以KB为单位），读/写权限，共享模式以及该区域中页面的用途。

The following sections show you how to interpret the output from the `vmmap` tool. For more information about the `vmmap` and `vmmap64` tools, see the `vmmap` or `vmmap64` man pages.

以下部分将介绍如何解释`vmmap`工具的输出。有关`vmmap`和`vmmap64`工具的更多信息，请参阅`vmmap`或`vmmap64`手册页。

### Sample Output From vmmap vmmap的示例输出
Listing 3 shows some sample output from the `vmmap` tool. This example is not a full listing of the tool’s output but is an abbreviated version showing the primary sections.

清单3显示了该`vmmap`工具的一些示例输出。此示例不是该工具输出的完整列表，而是显示主要部分的缩略版本。

**Listing 3**  Typical output of vmmap

**清单3** vmmap的典型输出

```
==== Non-writable regions for process 313
__PAGEZERO              0 [   4K] ---/--- SM=NUL ...ts/MacOS/Clock
__TEXT               1000 [  40K] r-x/rwx SM=COW ...ts/MacOS/Clock
__LINKEDIT           e000 [   4K] r--/rwx SM=COW ...ts/w/Clock
                    90000 [   4K] r--/r-- SM=SHM
                   340000 [3228K] r--/rwx SM=COW 00000100 00320...
                   789000 [3228K] r--/rwx SM=COW 00000100 00320...
Submap           90000000-9fffffff r--/r-- machine-wide submap
__TEXT           90000000  [ 932K] r-x/r-x SM=COW /usr/lib/libSystem.B.dylib
__LINKEDIT       900e9000   [ 260K] r--/r-- SM=COW /usr/lib/libSystem.B.dylib
__TEXT           90130000 [ 740K] r-x/r-x SM=COW .../Versions/A/CoreFoundation
__LINKEDIT       901e9000 [ 188K] r--/r-- SM=COW .../Versions/A/CoreFoundation
__TEXT           90220000 [2144K] r-x/r-x SM=COW .../Versions/A/CarbonCore
__LINKEDIT       90438000 [ 296K] r--/r-- SM=COW .../Versions/A/CarbonCore
 
[...data omitted...]
 
==== Writable regions for process 606
__DATA             18000 [   4K] rw-/rwx SM=PRV /Contents/MacOS/TextEdit
__OBJC             19000 [   8K] rw-/rwx SM=COW /Contents/MacOS/TextEdit
MALLOC_OTHER       1d000 [ 256K] rw-/rwx SM=PRV
MALLOC_USED(DefaultMallocZone_0x5d2c0)     5d000 [ 256K] rw-/rwx SM=PRV
                   9d000 [ 372K] rw-/rwx SM=COW 33320000 00000020 00000000 00001b84...
VALLOC_USED(DefaultMallocZone_0x5d2c0)     ff000 [  36K] rw-/rwx SM=PRV
MALLOC_USED(CoreGraphicsDefaultZone_0x10  108000 [ 256K] rw-/rwx SM=PRV
MALLOC_USED(CoreGraphicsRegionZone_0x148  148000 [ 256K] rw-/rwx SM=PRV
 
[...data omitted...]
 
Submap           a000b000-a012ffff r--/r-- process-only submap
__DATA           a0130000 [  28K] rw-/rw- SM=COW .../Versions/A/CoreFoundation
Submap           a0137000-a021ffff r--/r-- process-only submap
__DATA           a0220000 [  20K] rw-/rw- SM=COW .../Versions/A/CarbonCore
Submap           a0225000-a048ffff r--/r-- process-only submap
__DATA           a0490000 [  12K] rw-/rw- SM=COW .../IOKit.framework/Versions/A/IOKit
Submap           a0493000-a050ffff r--/r-- process-only submap
__DATA           a0510000 [  36K] rw-/rw- SM=COW .../Versions/A/OSServices
                 b959e000 [   4K] rw-/rw- SM=SHM
                 b95a0000 [   4K] rw-/rw- SM=SHM
                 b9630000 [ 164K] rw-/rw- SM=SHM
                 b965a000 [ 896K] rw-/rw- SM=SHM
                 bff80000 [ 504K] rw-/rwx SM=ZER
STACK[0]         bfffe000 [   4K] rw-/rwx SM=PRV
                 bffff000 [   4K] rw-/rwx SM=PRV
__DATA           c000c000 [   4K] rw-/rwx SM=PRV .../Versions/A/ApplicationEnhancer
STACK[1]         f0001000 [ 512K] rw-/rwx SM=PRV
                 ff002000 [12272K] rw-/rw- SM=SHM
 
==== Legend
SM=sharing mode:
    COW=copy_on_write PRV=private NUL=empty ALI=aliased
    SHM=shared ZER=zero_filled S/A=shared_alias
 
==== Summary for process 313
ReadOnly portion of Libraries: Total=27420KB resident=12416KB(45%) swapped_out_or_unallocated=15004KB(55%)
Writable regions: Total=21632KB written=536KB(2%) resident=1916KB(9%) swapped_out=0KB(0%) unallocated=19716KB(91%)
```
If you specify the `-d` parameter (plus an interval in seconds), `vmmap` takes two snapshots of virtual-memory usage—one at the beginning of a specified interval and the other at the end—and displays the differences. It shows three sets of differences:

如果您指定`-d`参数（加上一个以秒为单位的间隔），`vmmap`需要两个虚拟内存使用快照-一个在在指定间隔的开始，另一个在结尾-并且显示差异。它显示三组差异：

* individual differences 

	个体差异
	
* regions in the first snapshot that are not in the second

	在第一个快照中但不在第二个中的区域

* regions in the second snapshot that are not in the first

	在第二个快照中但不在第一个中的区域
### Interpreting vmmap’s Output 解释vmmap的输出
The columns of `vmmap` output have no headings. Instead you can interpret the type of data in each column by its format. Table 1 describes these columns.

vmmap输出列没有标题。相反，您可以按其格式解释每列中的数据类型。表1描述了这些列。

**Table 1** Column descriptions for vmmap

**表1**   vmmap的列说明

|Column Number 列号| Example 例子| Description 描述|
|:------------|:--------|:------------|
|1|`__TEXT`, `__LINKEDIT`, `MALLOC_USED`, `STACK`, and so on|The purpose of the memory. This column can contain the name of a Mach-O segment or the memory allocation technique. 内存的目的。该列可以包含一个Mach-O段的名称或内存分配技术|
|2|`(DefaultMallocZone_0x5d2c0)`|If present, the zone used for allocation. 如果存在，用于分配的区域|
|3|`4eee000`|The virtual memory address of the region. 该区域的虚拟内存地址|
|4|`[ 124K]`|The size of the region, measured in kilobytes 该区域的大小，以KB为单位|
|5|`rw-/rwx`|Read, write and execution permissions for the region. The first set of flags specifies the current protection for the region. The second set of values specifies the maximum protection for the region. If an entry contains a dash (-), the process does not have the target permission. 该区域的读，写和可执行权限。第一组标志指定当前该区域的保护。第二组值指定该区域的最大保护。如果一个条目包含一个破折号(-)，该进程没有目标权限。|
|6|`SM=PRV`| Sharing mode for the region, either `COW` (copy-on-write), `PRV` (private), `NUL` (empty), `ALI` (aliased), or `SHM` (shared). 该区域的共享模式，`COW`（写时复制），`PRV`（私有），`NUL`（空），`ALI`（别名）或`SHM`（共享）。|
|7|`...ts/MacOS/Clock`|The end of the pathname identifying the executable mapped into this region of virtual memory. If the region is stack or heap memory, nothing is displayed in this column. 标识可映射到该虚拟内存区域的可执行文件的路径名的结尾。如果该区域是栈或堆内存，则此列中不显示任何内容。|

Column 1 identifies the purpose of the memory. A `__TEXT` segment contains read-only code and data. A `__DATA` segment contains data that may be both readable and writable. For allocated data, this column shows how the memory was allocated, such as on the stack, using `malloc`, and so on. For regions loaded from a library, the far right column shows the name of the library loaded into memory.

列1标识了内存的目的。一个`__TEXT`段包含只读的代码和数据。一个`__DATA`段包含了可能是可读并且可写的数据。对于已分配的数据，此列显示内存是如何分配的，例如在栈上，使用`malloc`等。对于从库中加载的区域，最右边的列显示加载到内存中的库的名称。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 

The size of the virtual memory region (column 4) represents the total size reserved for that region. This number may not reflect the actual number of memory pages allocated for the region. For example, calling `vm_allocate` reserves a set of memory pages but does not allocate any physical memory until the pages are actually touched. Similarly, a memory-mapped file may reserve a set of pages, but the system does not load pages until a read or write event occurs on the file.

虚拟内存区域（第4列）的大小表示那个区域保留的总大小。该数字可能不反映为该区域分配的实际内存页数。例如，调用`vm_allocate`保留一组内存页面，但是页面在真正访问之前不会分配任何物理内存。类似地，一个内存映射文件可能保留一组页面，但是在文件上发生读取或写入事件之前，系统不会加载页面。

The protection mode (column 5) describes the access restrictions for the memory region. A memory region contains separate flags for read, write, and execution permissions. Each virtual memory region has a current permission, and a maximum permission. In the output from `vmmap`, the current permission appears first followed by the maximum permission. Thus, if the permissions are `“r--/rwx“` the page is currently read-only but allows read, write, and execution access as its maximum allowed permissions. Typically, the current permissions do not permit writing to a region. However, these permissions may change under certain circumstances. For example, a debugger may request write access to a page in order to set a breakpoint.

保护模式（第5列）描述了内存区域的访问限制。一个内存区域包含读取，写入和执行权限的单独标志。每个虚拟内存区域具有当前权限和一个最大权限。在`vmmap`的输出中，当前权限首先出现，然后是最大权限。因此，如果权限是`“r--/rwx”`，页面当前是只读的，但允许读取，写入和执行访问作为其最大允许权限。通常，当前权限不允许写入到一个区域。但是，这些权限在某些情况下可能会发生变化。例如，调试器可能请求对一个页面的写入访问以设置断点。

> **Note:** Pages representing part of a Mach-O executable are usually not writable. The first page (`__PAGEZERO`, starting at address `0x00000000`) has no permissions set. This ensures that any reference to a `NULL` pointer immediately causes an error. The page just before the stack is similarly protected so that stack overflows cause the application to crash immediately.
> 
> **注意：**表示Mach-O可执行文件的一部分的页面通常不可写。第一页（`__PAGEZERO`,从地址`0x00000000`开始）没有设置权限。这确保任何对`NULL`指针的引用会立即引起错误。该栈之前的页面也受到类似的保护，因此栈溢出会导致应用程序立即崩溃。

The sharing mode (`SM=` field) tells you whether pages are shared between processes and what happens when pages are modified. Private pages (`PRV`) are visible only to the process and are allocated as they are used. Private pages can also be paged out to disk. Copy-on-write (`COW`) pages are shared by multiple processes (or shared by a single process in multiple locations). When the page is modified, the writing process then receives its own copy of the page. Empty (`NUL`) sharing implies that the page does not really exist in physical memory. Aliased (`ALI`) and shared (`SHM`) memory are shared between processes.

共享模式（`SM=`字段）告诉您页面是否在进程之间共享，以及页面被修改时会发生什么。私有页面（`PRV`）仅对该进程可见，并在使用时分配。私有页面也可以被页出到磁盘。写时复制（`COW`）页面由多个进程（或由多个位置的单个进程共享）共享。当页面被修改时，写入进程接收到该页面自己的副本。空的（`NUL`）共享意味着该页面实际上不存在于物理内存中。别名（`ALI`）和共享（`SHM`）内存在进程之间共享。

The sharing mode typically describes the general mode controlling the region. For example, as copy-on-write pages are modified, they become private to the application. However, the region containing those private pages is still copy-on-write until all pages become private. Once all pages are private, the sharing mode changes to private.

共享模式通常描述控制该区域的一般模式。例如，当写时复制页面被修改时，它们对于应用程序变为私有的。但是，包含那些私有页面的区域仍然是写时复制的，直到所有页面都变为私有的。一旦所有页面都是私有的，共享模式将变为私有模式。

Some lines in the output of `vmmap` describe submaps. A submap is a shared set of virtual memory page descriptions that the operating system can reuse between multiple processes. For example, the memory between `0x90000000` and `0xAFFFFFFF` is a submap containing the most common dynamic libraries. Submaps minimize the operating system’s memory usage by representing the virtual memory regions only once. Submaps can either be shared by all processes (machine-wide) or be local to the process (process-only). If the contents of a machine-wide submap are changed—for example, the debugger makes a section of memory for a dynamic library writable so it can insert debugging traps—then the submap becomes local, and the kernel allocates memory to store the extra copy.

在`vmmap`输出的一些行描述了子映射。一个子映射是一组虚拟内存页面，描述了操作系统可以在多个进程之间重用。例如，在`0x90000000`和`0xAFFFFFFF`之间的内存是一个包含最常见动态库子映射。子映射通过仅将虚拟内存区域表示一次来最小化操作系统的内存使用。子映射可以由所有进程（机器范围）共享，也可以是该进程（仅进程）的本地。如果一个机器范围子映射的内容发生变化 - 例如，调试器使一个动态库的一个内存区域可写，以便它可以插入调试陷阱 - 然后子映射将变为本地，并且内核会分配内存以存储额外的副本。