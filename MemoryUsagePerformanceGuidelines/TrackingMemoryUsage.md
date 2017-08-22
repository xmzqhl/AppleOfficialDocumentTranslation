# Tracking Memory Usage 跟踪内存使用
If you suspect your code is not using memory as efficiently as it could, the first step in determining if there is a problem is to gather some baseline data. Monitoring your code using one of the Apple-provided performance tools can give you a picture of your code’s memory usage and may highlight potential problems or point to areas that need further examination. The following sections describe the tools most commonly used for memory analysis and when you might want to use them.

如果您怀疑您的代码没有尽可能高效地使用内存，那么确定是否存在问题的第一步是收集一些基准数据。使用一个Apple提供的性能工具监控代码，可以给您一个代码内存使用情况的图像，并可能高亮显示潜在的问题，或指向需要进一步检查的区域。以下部分介绍常用于内存分析的工具，以及何时您可能想要使用它们。
## Tracking Allocations With Instruments 使用Instruments跟踪分配
The Instruments application is always a good starting point for doing any sort of performance analysis. Instruments is an integrated, data-gathering environment that uses special modules (called instruments) to gather all sorts of information about a process. Instruments can operate on your application’s shipping binary file—you do not need to compile special modules into your application to use it. The library of the Instruments application contains four modules that are designed specifically for gathering memory-related data. These modules are as follows:

Instruments应用程序始终是进行任何性能分析的良好起点。Instruments是一个集成的，数据收集环境，使用特殊模块(称为仪器)来收集有关进程的各种信息。Instruments可以在您应用程序的运输(运输一词，暂未想到合适的解释)二进制文件上操作-您不需要为了使用它将特殊的模块编译到您应用程序中。Instruments应用程序的库包含四个专门用于收集内存相关数据的模块。这些模块如下：

* The ObjectAlloc instrument records and displays a history of all memory allocations since the launch of your application.

	ObjectAlloc仪器记录和显示自从您应用程序启动后的所有内存分配的历史记录。

* The Leaks instrument looks for allocated memory that is no longer referenced by your program’s code; see [Finding Leaks Using Instruments](https://developer.apple.com/library/content/documentation/Performance/Conceptual/ManagingMemory/Articles/FindingLeaks.html#//apple_ref/doc/uid/20001883-SW2).

	Leaks仪器会查找不再被您项目代码引用的已分配的内存。请参阅[使用Instruments查找内存泄露](Finding Memory Leaks.md)
* The Shared Memory instrument monitors the opening and unlinking of shared memory regions.

	Shared Memory仪器监控共享内存区域的打开和取消链接。
* The Memory Monitor instrument measures and records the system’s overall memory usage.

	Memory Monitor仪器测量和记录系统的整体内存使用情况。

You can add any or all of these instruments to a single trace document and gather data for each of them simultaneously. Being able to gather the data all at once lets you correlate information from one instrument with the others. For example, the Leaks instrument is often combined with the ObjectAlloc instrument so that you can both track the allocations and find out which ones were leaked.

您可以将任何或所有这些仪器添加到单个跟踪文件中，并同时收集每个文件的数据。能够一次收集所有的数据，使您可以把一个仪器的信息与其他仪器相关联。例如，Leaks仪器通常与ObjectAlloc仪器相结合，以便您可以同时跟踪分配情况，并找出哪个被泄露了。

After gathering data for your application, you need to analyze it. The following sections provide tips on how to analyze data using several of the memory-related instruments. For information on how to use the Leaks instrument, see [Finding Leaks Using Instruments](https://developer.apple.com/library/content/documentation/Performance/Conceptual/ManagingMemory/Articles/FindingLeaks.html#//apple_ref/doc/uid/20001883-SW2).

收集您应用程序的数据之后，您需要对其进行分析。以下部分提供了如何使用几个内存相关的仪器分析数据的技巧。有关如何使用Leaks仪器的信息，请参阅[使用Instruments查找泄漏](Finding Memory Leaks.md)。
### Analyzing Memory Allocations with the ObjectAlloc Instrument 使用ObjectAlloc仪器分析内存分配
You use the ObjectAlloc instrument to track the memory allocation activity for your application. This instrument tracks memory allocations from the time your application is launched until you stop recording data. The instrument shows you the allocations in terms of the number of objects created and the size (or type) of the objects that were created. In the icon viewing mode, the instrument displays a real-time histogram that lets you see changes and trends directly as they occur. It also retains a history of allocations and deallocations, giving you an opportunity to go back and see where those objects were allocated.

您可以使用ObjectAlloc工具来为您的应用程序跟踪内存分配活动。该仪器会从您应用程序的启动时间到停止记录数据的时间段内跟踪内存分配。仪器根据创建的对象的数量和大小(或类型)来显示分配。在图标查看模式下，仪器将显示一个实时柱状图，让您直接看到他们发生时的变化和趋势。它还保留了分配和释放的历史，让您有机会返回来查看那些对象分配的位置。

The information displayed by the ObjectAlloc instrument is recorded by an allocation statistics facility built into the Core Foundation framework. When this facility is active, every allocation and deallocation is recorded as it happens.

由ObjectAlloc仪器显示的信息由Core Foundation框架中内置的分配统计工具记录。当该工具处于活动状态时，每次分配和解除分配都会记录下来。
### Analyzing Shared Memory Usage 分析共享内存使用情况
For Mac apps, the Shared Memory instrument tracks calls to any `shm_open` and `shm_unlink` functions, which are used for opening and closing regions of shared memory in the system. You can use this information to find out where your application is getting references to shared memory regions and to examine how often those calls are being made. The detail pane displays the list of each function call along with information about the calling environment at the time of the call. Specifically, the pane lists the executable that initiated the call and the function parameters. Opening the Extended Detail pane shows the stack trace associated with the call.

对于Mac应用程序，Shared Memory仪器跟踪对任何`shm_open`和`shm_unlink`函数的调用，这些函数用于打开和关闭系统中共享内存的区域。您可以使用此信息来找出应用程序在哪获取对共享内存区域的引用，并检查那些调用的频率。详细信息窗格将显示每个函数调用的列表以及调用时的调用环境信息。具体来说，窗格列出了初始化调用和函数参数的可执行文件。打开“扩展详细信息”窗格显示与调用相关联的栈跟踪。

### Analyzing Data from the Memory Monitor Instrument 从Memory Monitor中分析数据
For Mac apps, the Memory Monitor instrument displays assorted statistics about system memory usage. You can use this instrument to view the memory usage trends in your application or on the entire system. For example, you can see how much memory is currently active, inactive, wired, and free. You can also see the amount of memory that has been paged in or out. You might use this instrument in conjunction with other instruments to track the amount of memory your application uses with respect to specific operations.

对于Mac应用程序，Memory Monitor仪表显示有关系统内存使用的分类统计。您可以使用此仪器查看应用程序或整个系统中的内存使用趋势。例如，您可以看到当前有效，无效，联动和空闲的内存量。您还可以查看已经页入或页出的内存量。您可以将此仪器与其他仪器一起使用，以跟踪应用程序对特定操作方面所使用的内存量。
## Tracking Memory Allocations With malloc_history 使用malloc_history跟踪内存分配
In OS X, the `malloc_history` tool displays backtrace data showing exactly where your program made calls to the `malloc` and `free` functions. If you specify an address when calling `malloc_history`, the tool tracks memory allocations occurring at that address only. If you specify the `-all_by_size` or `-all_by_count` options, the tool displays all allocations, grouping frequent allocations together.

在OS X中，`malloc_history`工具显示调用栈数据准确显示你的项目在哪调用`malloc`和`free`函数。如果您在调用`malloc_history`时指定了一个地址，则该工具仅跟踪在该地址处发生的内存分配。如果指定`-all_by_size`或`-all_by_count`选项，该工具将显示所有的分配，将频繁的分配分组在一起。

Before using the `malloc_history` tool on your program, you must first enable the malloc library logging features by setting the `MallocStackLogging` to `1`. You may also want to set the `MallocStackLoggingNoCompact` environment variable to retain information about freed blocks. For more information on these variables, see [Enabling the Malloc Debugging Features](https://developer.apple.com/library/content/documentation/Performance/Conceptual/ManagingMemory/Articles/MallocDebug.html#//apple_ref/doc/uid/20001884-CJBJFIDD).

在您的项目上使用`malloc_history`工具之前，您必须首先通过设置`MallocStackLogging`到`1`来启用malloc库记录功能。你也可以设置`MallocStackLoggingNoCompact`环境变量来保留有关已释放块的信息。有关这些变量的更多信息，请参阅[启用Malloc调试功能](Enabling the Malloc Debugging Features.md)。

The `malloc_history` tool is best used in situations where you need to find the previous owner of a block of memory. If you determine that a particular data is somehow becoming corrupted, you can put checks into your code to print the address of the block when the corruption occurs. You can then use `malloc_history` to find out who owns the block and identify any stale pointers.

该`malloc_history`工具最适用于需要查找内存块的先前的拥有者的情况。如果您确定某个特定的数据被破坏了，您可以将检查插入到代码中，以便在发生损坏时打印块的地址。然后，您可以使用`malloc_history`来找出谁拥有该块，并识别任何陈旧的指针。

The `malloc_history` tool is also suited for situations where Sampler cannot be used. For example, you might use this tool from a remote computer or in situations where you want a minimal impact on the behavior of your program.

该`malloc_history`工具也适用于无法使用取样器(Sampler)的情况。例如，您可以从一个远程计算机上使用此工具，或者您希望对程序行为影响最小的情况。

For more information on using the `malloc_history` tool, see `malloc_history` man page.

有关使用该`malloc_history`工具的更多信息，请参阅`malloc_history`手册页。
## Examining Memory With the heap Tool 使用heap工具检查内存
In OS X, the `heap` command-line tool displays a snapshot of the memory allocated by the malloc library and located in the address space of a specified process. For Cocoa applications, this tool identifies Objective-C objects by name. For both memory blocks and objects, the tool organizes the information by heap, showing all items in the same heap together.

在OS X中，`heap`命令行工具显示由malloc库分配并位于指定进程的地址空间中的内存的快照。对于Cocoa应用程序，此工具通过名称来识别Objective-C对象。对于内存块和对象，该工具通过堆来组织信息，把同一堆中的所有项目显示在一起。

The `heap` tool provides much of the same information as the ObjectAlloc instrument, but does so in a much less intrusive manner. You can use this tool from a remote session or in situations where the use of Instruments might slow the system down enough to affect the resulting output.

该`heap`工具提供与ObjectAlloc仪器大量相同的信息，但是以非常少的侵入方式做这些。您可以从远程会话中使用此工具，或者在使用Instruments可能会使系统下降到足以影响结果输出的情况下使用此工具。

For more information about using the `heap` tool, see `heap(1)` man page.

有关使用该`heap`工具的更多信息，请参阅`heap(1)`手册页。

