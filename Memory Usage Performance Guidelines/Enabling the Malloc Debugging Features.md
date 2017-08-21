# Enabling the Malloc Debugging Features 启用Malloc调试功能
Debugging memory-related bugs can be time consuming if you do not know where to start looking. This is usually compounded by the problem that many memory bugs occur well after the memory in question was manipulated by the code. Fortunately, Xcode includes options for identifying memory problems closer to when those problems actually happen.

如果您不知道从哪里开始查找，调试内存相关的错误可能会耗费时间。这通常被复杂化，由于许多内存错误发生在有问题的内存被代码操纵之后的问题。幸运的是，Xcode包含了选项来识别更接近于那些问题实际发生的时间的内存错误。

## Enabling Guard Malloc 启用Guard Malloc
Guard Malloc is a special version of the malloc library that replaces the standard library during debugging. Guard Malloc uses several techniques to try and crash your application at the specific point where a memory error occurs. For example, it places separate memory allocations on different virtual memory pages and then deletes the entire page when the memory is freed. Subsequent attempts to access the deallocated memory cause an immediate memory exception rather than a blind access into memory that might now hold other data. When the crash occurs, you can then go and inspect the point of failure in the debugger to identify the problem.

Guard Malloc是malloc库的一个特殊版本，可在调试期间替换标准库。Guard Malloc使用多种技术在发生内存错误的时间点尝试和崩溃应用程序。例如，它会在不同的虚拟内存页面上放置隔离的内存分配，然后在内存被释放时删除整个页面。随后，尝试访问已释放的内存引起一个立即内存异常，而不是盲目访问可能现在持有其他数据的内存。当崩溃发生时，您可以检查调试器中的故障点来确定问题。

To enable debugging using Guard Malloc, configure your project to run with Guard Malloc in the [Xcode Scheme Editor](http://help.apple.com/xcode/mac/9.0/).

要使用Guard Malloc启用调试，请将您的项目配置为在[Xcode Scheme Editor](http://help.apple.com/xcode/mac/9.0/)中用Guard Malloc运行

You can use this option for Mac apps and iOS apps running in the simulator.

您可以对Mac应用和在模拟器中运行的iOS应用程序使用此选项。

For more information about the types of memory problems that Guard Malloc can help you track down, see the `libgmalloc` man page in OS X Man Pages.

有关Guard Malloc可以帮助您跟踪的内存问题的类型的更多信息，请参阅OS X手册页中的`libgmalloc`手册页。

## Configuring the Malloc Environment Variables 配置Malloc环境变量
The malloc library provides debugging features to help you track down memory smashing bugs, heap corruption, references to freed memory, and buffer overruns. You enable these debugging options through a set of environment variables. With the exception of `MallocCheckHeapStart` and `MallocCheckHeapEach`, the value for most of these environment variables is ignored. To disable a variable from Terminal, use the `unset` command. Table 1 lists some of the key environment variables and describes their basic function. For a complete list of variables, see the `malloc` man page.

malloc库提供调试功能，以帮助您跟踪内存崩溃的错误，堆损坏，对释放的内存的引用以及缓冲区溢出。通过一组环境变量启用这些调试选项。除了`MallocCheckHeapStart`和`MallocCheckHeapEach`，大多数这些环境变量的值被忽略。要从终端禁用一个变量，请使用`unset`命令。表1列出了一些关键环境变量，并描述了其基本功能。有关变量的完整列表，请参阅`malloc`手册页。

**Table 1**  Malloc environment variables

**表1** Malloc环境变量

| Variable(变量) | Description(描述)|
|:--------------|:----------------|
| MallocStackLogging |If set, `malloc` remembers the function call stack at the time of each allocation. 如果设置，`malloc`在记录每次分配时的函数调用堆栈。|
| MallocStackLoggingNoCompact |This option is similar to `MallocStackLogging` but makes sure that all allocations are logged, no matter how small or how short lived the buffer may be. 此选项类似于`MallocStackLogging`,但是确保所有分配都被记录，无论缓冲区可能有多小或多短暂。|
| MallocScribble |If set, `free` sets each byte of every released block to the value `0x55`. 如果设置，`free`将每个已释放块的每个字节设置为`0x55`。|
| MallocPreScribble |If set, `malloc` sets each byte of a newly allocated block to the value `0xAA`. This increases the likelihood that a program making assumptions about freshly allocated memory fails. 如果设置，`malloc`则将新分配的块的每个字节设置为`0xAA`。这增加了对新分配内存进行假定的项目失败可能性。|
| MallocGuardEdges |If set, `malloc` adds guard pages before and after large allocations. 如果设置，则`malloc`在大量分配之前和之后添加保护页面。|
| MallocDoNotProtectPrelude | Fine-grain control over the behavior of `MallocGuardEdges:` If set, `malloc` does not place a guard page at the head of each large block allocation. 在`MallocGuardEdges：`之上的细粒度控制,如果设置，`malloc`则不会在每个大块分配的头部放置一个保护页面。|
| MallocDoNotProtectPostlude |Fine-grain control over the behavior of `MallocGuardEdges:` If set, `malloc` does not place a guard page at the tail of each large block allocation. 在`MallocGuardEdges：`之上的细粒度控制,如果设置，`malloc`则不会在每个大块分配的尾部放置一个保护页面。|
| MallocCheckHeapStart |Set this variable to the number of allocations before `malloc` will begin validating the heap. If not set, `malloc` does not validate the heap. 将此变量设置为`malloc`将要开始验证堆之前的分配数。如果未设置，`malloc`则不会验证堆。|
| MallocCheckHeapEach |Set this variable to the number of allocations before `malloc` should validate the heap. If not set, `malloc` does not validate the heap. 将此变量设置为`malloc`应验证堆之前的分配数。如果未设置，malloc则不会验证堆。|

The following example enables stack logging and heap checking in the current shell before running an application. The value for `MallocCheckHeapStart` is set to 1 but is irrelevant and can be set to any value you want. You could also set these variables from your shell’s startup file, although if you do be sure to `export` each variable.

以下示例在运行应用程序之前在当前shell中启用堆栈记录和堆检查。`MallocCheckHeapStart`的值设置为1，但不相关，并可以设置为您想要的任何值。您也可以从您的shell启动文件中设置这些变量，但是，如果您这么做，请确保`export`每个变量。

```
% MallocStackLogging=1
% MallocCheckHeapStart=1000
% MallocCheckHeapEach=100
% ./my_tool
```
If you want to run your program in `gdb`, you can set environment variables from the Xcode debugging console using the command `set env`, as shown in the following example:

如果您想要在`gdb`中运行您的项目，您可以从Xcode调试控制台中使用`set env`命令来设置环境变量，如下面的示例所示：

```
% gdb
(gdb) set env MallocStackLogging 1
(gdb) run
```
Some of the performance tools require these options to be set in order to gather their data. For example, the `malloc_history` tool can identify the allocation site of specific blocks if the `MallocStackLogging` flag is set. This tool can also describe the blocks previously allocated at an address if the `MallocStackLoggingNoCompact` environment variable is set. The `leaks` command line tool will name the allocation site of a leaked buffer if `MallocStackLogging` is set. See the man pages for `leaks` and `malloc_history` for more details.

一些性能工具需要设置这些选项才能收集数据。例如，如果`MallocStackLogging`标志被设置，则`malloc_history`工具可以识别特定块的分配位置。如果`MallocStackLoggingNoCompact`环境变量被设置了，此工具还可以描述先前在一个地址中分配的块。如果`MallocStackLogging`被设置了，`leaks`命令行工具将命名泄露缓冲区的分配位置。请参阅`leaks`和`malloc_history`的手册页获得更多的细节。

### Detecting Double Freed Memory 检测双重释放内存
The malloc library reports attempts to call `free` on a buffer that has already been freed. If you have enabled the `MallocStackLoggingNoCompact` option, you can use the logged stack information to find out where in your code the second `free` call was made. You can then use this information to set up an appropriate breakpoint in the debugger and track down the problem.

malloc库报告在已经释放的缓冲区上尝试调用`free`的操作。如果您已经启用了该`MallocStackLoggingNoCompact`选项，那么您可以使用记录的堆栈信息来查找代码中第二次调用`free`的位置。然后，您可以使用此信息在调试器中设置适当的断点，并跟踪这个问题。

The malloc library reports information to `stderr`.

malloc库报告信息给`stderr`。

### Detecting Heap Corruption 检测堆损坏
To enable heap checking, assign values to the `MallocCheckHeapStart` and `MallocCheckHeapEach` environment variables. You must set both of these variables to enable heap checking. The `MallocCheckHeapStart` variable tells the malloc library how many `malloc` calls to process before initiating the first heap check. Set the second to the number of `malloc` calls to process between heap checks.

要启用堆检查，赋值给`MallocCheckHeapStart`和`MallocCheckHeapEach`环境变量。您必须设置这两个变量以启用堆检查。该`MallocCheckHeapStart`变量告诉malloc库在开始第一个堆检查之前需要处理多少个`malloc`调用。将第二个设置为堆检查之间要处理的`malloc`调用数。

The `MallocCheckHeapStart` variable is useful when the heap corruption occurs at a predictable time. Once it hits the appropriate start point, the malloc library starts logging allocation messages to the Terminal window. You can watch the number of allocations and use that information to determine approximately where the heap is being corrupted. Adjust the values for `MallocCheckHeapStart` and `MallocCheckHeapEach` as necessary to narrow down the actual point of corruption.

当在可预测的时间发生堆损坏时,`MallocCheckHeapStart`变量是有用的。一旦达到适当的起始点，malloc库就会开始将分配信息记录到“终端”窗口。您可以查看分配的数量，并使用该信息来确定堆的大致损坏位置。根据需要调整`MallocCheckHeapStart`和`MallocCheckHeapEach`的值，以缩小实际的损坏点。

### Detecting Memory Smashing Bugs 检测内存粉碎错误
To find memory smashing bugs, enable the `MallocScribble` variable. This variable writes invalid data to freed memory blocks, the execution of which causes an exception to occur. When using this variable, you should also set the `MallocStackLogging` and `MallocStackLoggingNoCompact` variables to log the location of the exception. When the exception occurs, you can then use the `malloc_history` command to track down the code that allocated the memory block. You can then use this information to track through your code and look for any lingering pointers to this block.

要找到内存粉碎的错误，启用`MallocScribble`变量。该变量将无效数据写入已释放的内存块，这些数据的执行会导致异常发生。使用此变量时，您还应该设置`MallocStackLogging`和`MallocStackLoggingNoCompact`变量来记录异常的位置。当异常发生时，您可以使用该`malloc_history`命令来跟踪分配该内存块的代码。然后，您可以使用此信息跟踪代码，并查找该块的任何逗留指针。
