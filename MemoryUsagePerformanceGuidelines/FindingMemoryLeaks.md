# Finding Memory Leaks 查找内存泄露
Memory leaks are blocks of allocated memory that the program no longer references. Leaks waste space by filling up pages of memory with inaccessible data and waste time due to extra paging activity. Leaked memory eventually forces the system to allocate additional virtual memory pages for the application, the allocation of which could have been avoided by reclaiming the leaked memory.

内存泄漏是程序不再引用的已分配内存块。泄露通过用不可访问的数据填充内存页来浪费空间，并且由于额外的分页活动浪费时间。泄漏的内存最终会迫使系统为应用程序分配额外的虚拟内存页面，通过回收泄漏的内存可以避免这种分配。

For apps that use malloc, memory leaks are bugs and should always be fixed. For apps that use only Objective-C objects, the compiler’s ARC feature deallocates objects for you and generally avoids the problem of memory leaks. However, apps that mix the use of Objective-C objects and C-based structures must manage the ownership of objects more directly to ensure that the objects are not leaked.

对于使用malloc的应用程序，内存泄漏是错误，应该始终被修复。对于仅使用Objective-C对象的应用程序，编译器的ARC功能会为您回收对象，并且通常可以避免内存泄漏的问题。然而，混合使用Objective-C对象和基于C的结构的应用程序必须更直接地管理对象的所有权，以确保对象不会泄漏。

The malloc library can only reclaim the memory you tell it to reclaim. If you call `malloc` or any routine that allocates memory, you must balance that call with a corresponding `free`. A typical memory leak occurs when you forget to deallocate memory for a pointer embedded in a data structure. If you allocate memory for embedded pointers in your code, make sure you release the memory for that pointer prior to deallocating the data structure itself.

malloc库只能回收您告诉它要回收的内存。如果您调用`malloc`或任何分配内存的例程，您必须将该调用与对应的`free`相平衡。当您忘记为嵌入在数据结构中的指针释放内存时，会发生典型的内存泄漏。如果您在代码中嵌入指针来分配内存，请确保在释放数据结构本身之前释放该指针的内存。

Another typical memory leak example occurs when you allocate memory, assign it to a pointer, and then assign a different value to the pointer without freeing the first block of memory. In this example, overwriting the address in the pointer erases the reference to the original block of memory, making it impossible to release.

另一个典型的内存泄露的例子发生在，当您分配内存，将其指定给一个指针，然后在不释放第一个内存块的情况下为该指针分配一个不同的值。在这个例子中，覆盖指针中的地址会擦除对原始内存块的引用，从而使它不能释放。
## Finding Leaks Using Instruments 使用Instruments查找泄露
The Instruments application can be used to find leaks in both OS X and iPhone applications. To find leaks, create a new document template in Instruments and add the Leaks instrument to it. The Leaks instrument provides leak-detection capabilities identical to those in the `leaks` command-line tool. The Leaks instrument records all allocation events that occur in your application and then periodically searches the application’s writable memory, registers, and stack for references to any active memory blocks. If it does not find a reference to a block in one of these places, it deems the block a “leak” and displays the relevant information in the Detail pane.

Instruments应用程序可用于在OS X和iPhone应用程序中查找泄漏。要查找泄漏，请在Instruments中创建一个新的文件模板，并向其中添加Leaks仪器。Leaks仪器提供与`leaks`命令行工具相同的泄漏检测功能。Leaks仪器记录您应用程序中发生的所有分配事件，然后定期搜索应用程序引用任何活动内存块的可写内存，寄存器和栈。如果在这些位置之一找不到某个块的引用，则认为块为“泄漏”，并在“详细信息”窗格中显示相关信息。

In the Detail pane, you can view leaked memory blocks using Table and Outline modes. In Table mode, Instruments displays the complete list of leaked blocks, sorted by size. Selecting an entry in the table and clicking the arrow button next to the memory address shows the allocation history for the memory block at that address. Selecting an entry from this allocation history then shows the stack trace for that event in the Extended Detail pane of the document window. In Outline mode, the Leaks instrument displays leaks organized by call tree, which you can use to get information about the leaks in a particular branch of your code.

在“详细信息”窗格中，可以使用“表格(Table)”和“大纲(Outline)”模式查看泄漏的内存块。在表格模式下，Instruments显示按大小排序的泄漏块的完整列表。选择列表中的一个条目，并且点击内存地址旁边的箭头按钮显示该地址上的内存块的分配历史记录。从该分配历史中选择一个条目，然后会在文件窗口的“扩展详细信息(Extended Detail)”窗格中显示该事件的堆栈跟踪。在“大纲”模式下，Leaks仪器会显示通过调用树组织的泄漏，您可以使用它来获取有关代码特定分支中的泄漏信息。

For more information about using the Instruments application, including more information about the information displayed by the Leaks instrument, see [Instruments User Guide](https://developer.apple.com/library/content/documentation/DeveloperTools/Conceptual/InstrumentsUserGuide/index.html#//apple_ref/doc/uid/TP40004652).

有关使用Instruments应用程序的更多信息，包括有关Leaks仪器显示信息的更多信息，请参阅[Instruments用户指南](https://developer.apple.com/library/content/documentation/DeveloperTools/Conceptual/InstrumentsUserGuide/index.html#//apple_ref/doc/uid/TP40004652)。

## Using the leaks Tool 使用leaks工具
In OS X, the `leaks` command-line tool searches the virtual memory space of a process for buffers that were allocated by `malloc` but are no longer referenced. For each leaked buffer it finds, `leaks` displays the following information:

在OS X中，`leaks`命令行工具在进程的虚拟内存空间中搜索由`malloc`分配但不再被引用的缓冲区。对于它找到的每个泄漏的缓冲区，`leaks`显示以下信息：

* the address of the leaked memory 泄漏内存的地址
* the size of the leak (in bytes) 泄漏的大小（以字节为单位）
* the contents of the leaked buffer 泄漏的缓冲区的内容

If `leaks` can determine that the object is an instance of an Objective-C or Core Foundation object, it also displays the name of the object. If you do not want to view the contents of each leaked buffer, you can specify the `-nocontext` option when calling `leaks`. If the `MallocStackLogging` environment variable is set and you are running your application in `gdb`, `leaks` displays a stack trace describing where the buffer was allocated. For more information on `malloc` debugging options, see [Enabling the Malloc Debugging Features](https://developer.apple.com/library/content/documentation/Performance/Conceptual/ManagingMemory/Articles/MallocDebug.html#//apple_ref/doc/uid/20001884-CJBJFIDD).

如果`leaks`可以确定对象是一个Objective-C或Core Foundation对象的实例，它还将显示对象的名称。如果不想查看每个泄漏的缓冲区的内容，您可以在调用`leaks`时指定`-nocontext`选项。如果设置了`MallocStackLogging`环境变量并且您正在`gdb`中运行您的应用程序，则`leaks`显示一个堆栈跟踪来描述该缓冲区被分配的位置。有关`malloc`调试选项的更多信息，请参阅[启用Malloc调试功能](EnablingTheMallocDebuggingFeatures.md)
。

> **Note:** The leaks command-line tool is located in `/usr/bin`.
>
> **注意：** 该leaks命令行工具位于`/usr/bin`。

## Tips for Improving Leak Detection 提高泄漏检测的技巧
The following guidelines can help you find memory leaks quickly in your program. Most of these guidelines are intended to be used with the `leaks` tool but some are also applicable for general use.

以下准则可以帮助您在程序中快速找到内存泄漏。大多数这些准则旨在与该`leaks`工具一起使用，但有些也适用于一般用途。

* Run `leaks` during unit testing. Because unit testing exercises all code paths in a repeatable manner, you are more likely to find leaks than you would be if you were testing your code in a production environment.

	在单元测试期间运行`leaks`。因为单元测试以可重复的方式执行所有代码路径，所以您更有可能发现泄漏，而不是在生产环境中测试代码。

* Use the `-exclude` option of `leaks` to filter out leaks in functions with known memory leaks. This option helps reduce the amount of extraneous information reported by `leaks`.

	使用`leaks`的`-exclude`选项来过滤掉函数中的已知内存泄漏。此选项有助于减少被`leaks`报告的无关信息的数量。

* If `leaks` reports a leak intermittently, set up a loop around the target code path and run the code hundreds or thousands of times. This increases the likelihood of the leak reappearing more regularly.

	如果`leaks`间歇地报告泄漏，请在目标代码路径周围设置一个循环，并运行代码数百或数千次。这增加了泄漏再次出现的可能性。

* Run your program against `libgmalloc.dylib` in `gdb`. This library is an aggressive debugging malloc library that can help track down insidious bugs in your code. For more information, see the `libgmalloc` man page.

	在`gdb`中针对`libgmalloc.dylib`运行您的程序。该库是一个积极的调试malloc库，可以帮助跟踪您的代码中的隐藏错误。更多信息，请参阅`libgmalloc`手册页。

* For Cocoa and iPhone applications, if you fix a leak and your program starts crashing, your code is probably trying to use an already-freed object or memory buffer. Set the `NSZombieEnabled` environment variable to `YES` to find messages to already freed objects.

	对于Cocoa和iPhone应用程序，如果您修复一个泄漏并且您的程序开始崩溃，您的代码可能正在尝试使用已经释放的对象或内存缓冲区。将`NSZombieEnabled`环境变量设置`YES`来查找已经释放的对象的消息。

Most unit testing code executes the desired code paths and exits. Although this is perfectly normal for unit testing, it creates a problem for the `leaks` tool, which needs time to analyze the process memory space. To fix this problem, you should make sure your unit-testing code does not exit immediately upon completing its tests. You can do this by putting the process to sleep indefinitely instead of exiting normally.

大多数单元测试代码执行所需的代码路径并退出。虽然这对于单元测试来说是完全正常的，但是它会为`leaks`工具制造一个问题，这需要时间来分析进程内存空间。要修复这个问题，您应该确保您的单元测试代码在完成测试后不会立即退出。您可以通过将进程无限期地休眠，而不是正常退出来做到这个。