# Tips for Allocating Memory 分配内存的技巧
Memory is an important resource for your application so it’s important to think about how your application will use memory and what might be the most efficient allocation approaches. Most applications do not need to do anything special; they can simply allocate objects or memory blocks as needed and not see any performance degradation. For applications that use large amount of memory, however, carefully planning out your memory allocation strategy could make a big difference.

内存是您应用程序的重要资源，因此，考虑您的应用程序将如何使用内存和最有效的分配方法是什么，这是比较重要的。大多数应用程序不需要做任何特殊的操作；它们可以根据需要简单地分配对象或内存块，而不会看到任何性能的下降。然后，对于使用大量内存的应用程序来说，仔细规划您的内存分配策略可能会产生很大的影响。

The following sections describe the basic options for allocating memory along with tips for doing so efficiently. To determine if your application has memory performance problems in the first place, you need to use the Xcode tools to look at your application’s allocation patterns while it is running. For information on how to do that, see [Tracking Memory Usage](https://developer.apple.com/library/content/documentation/Performance/Conceptual/ManagingMemory/Articles/FindingPatterns.html#//apple_ref/doc/uid/20001882-CJBJFIDD).

以下部分描述了分配内存的基本选项以及高效分配内存的技巧。要确定您的应用程序是否有内存性能问题，首先，在您应用运行的时候，需要使用Xcode工具来查看它的分配模式。有关如何执行此操作的信息，请参阅[跟踪内存使用](Tracking Memory Usage.md)
## Tips for Improving Memory-Related Performance 提高内存相关性能的技巧
As you design your code, you should always be aware of how you are using memory. Because memory is an important resource, you want to be sure to use it efficiently and not be wasteful. Besides allocating the right amount of memory for a given operation, the following sections describe other ways to improve the efficiency of your program’s memory usage.

在设计你的代码时，您应该始终了解如何使用内存。因为内存是一个重要的资源，所以你要确保高效使用它和不浪费。除了为给定的操作分配适量的内存之外，以下部分介绍了其他方法来提供内存使用的效率。
### Defer Your Memory Allocations 延迟你的内存分配
Every memory allocation has a performance cost. That cost includes the time it takes to allocate the memory in your program’s logical address space and the time it takes to assign that address space to physical memory. If you do not plan to use a particular block of memory right away, deferring the allocation until the time when you actually need it is the best course of action. For example, to avoid the appearance of your app launching slowly, minimize the amount of memory you allocate at launch time. Instead, focus your initial memory allocations on the objects needed to display your user interface and respond to input from the user. Defer other allocations until the user issues starts interacting with your application and issuing commands. This lazy allocation of memory saves time right away and ensures that any memory that is allocated is actually used.

每次内存分配都有性能成本。该成本包括在程序的逻辑地址空间中分配内存所需的时间，以及将该地址空间分配给物理内存所需的时间。如果你并不打算立即使用特定的内存块，推迟分配直到你真的需要它的时间点是最明智的做法。例如， 为了避免您的应用程序出现启动缓慢，最小化在启动时的内存分配量。相反，将初始内存分配集中在显示用户界面所需的对象上，并响应用户的输入。延迟其他的分配，直到用户真的开始与你的应用交互并发出指令。这种内存的懒加载会立即节省时间，并确保任何分配的内存是实际会被使用的。

Once place where lazy initialization can be somewhat tricky is with global variables. Because they are global to your application, you need to make sure global variables are initialized properly before they are used by the rest of your code. The basic approach often taken with global variables is to define a static variable in one of your code modules and use a public accessor function to get and set the value, as shown in Listing 1.

懒加载可能有点棘手的一个地方是使用全局变量。因为它们对你应用程序来说是全局的，所以，你需要确保全局变量在其余代码使用之前已经正确的初始化。通常采用全局变量的基本方法是在一个代码模块中定义一个静态变量，并使用公共的访问器函数来获取和设置该值，如列表1所示。

**Listing 1**  Lazy allocation of memory through an accessor

**列表1** 通过一个访问器懒分配内存

```
MyGlobalInfo* GetGlobalBuffer()  
{
    static MyGlobalInfo* sGlobalBuffer = NULL;
    if ( sGlobalBuffer == NULL )
        {
            sGlobalBuffer = malloc( sizeof( MyGlobalInfo ) );
        }
        return sGlobalBuffer;
}
```
The only time you have to be careful with code of this sort is when it might be called from multiple threads. In a multithreaded environment, you need to use locks to protect the `if` statement in your accessor method. The downside to that approach though is that acquiring the lock takes a nontrivial amount of time and must be done every time you access the global variable, which is a performance hit of a different kind. A simpler approach would be to initialize all global variables from your application’s main thread before it spawns any additional threads.

你唯一需要小心这种代码的时候是它可能从多线程调用。在一个多线程环境中，你需要锁来保护你访问器方法中的`if`语句。但是，这种方法的缺点在于，获取锁需要非常多的时间，并且你每次访问全局变量的时候都必须执行，这是一种不同类型的性能影响。一个更简单的方式是在你的应用程序生成其他任何线程之前，从应用的主线程初始化所有的全局变量。
### Initialize Memory Blocks Efficiently 有效的初始化内存块
Small blocks of memory, allocated using the `malloc` function, are not guaranteed to be initialized with zeroes. Although you could use the `memset` function to initialize the memory, a better choice is to use the `calloc` routine to allocate the memory in the first place. The `calloc` function reserves the required virtual address space for the memory but waits until the memory is actually used before initializing it. This approach is much more efficient than using `memset`, which forces the virtual memory system to map the corresponding pages into physical memory in order to zero-initialize them. Another advantage of using the `calloc` function is that it lets the system initialize pages as they’re used, as opposed to all at once.

使用`malloc`函数分配的小块内存，不被保证会用0初始化。尽管你可以使用`memset `函数来初始化内存，但更好的选择是使用`calloc`例程分配内存。`calloc`函数储存内存所需的虚拟地址空间，但是，一直等待直到该内存在初始化它之前实际被使用的时候。这种方法比使用`memset`更有效率，这迫使虚拟内存系统将对应的页面映射到物理内存中，以便对其进行0初始化。使用`calloc`函数的另一个好处是，它让系统在页面被使用的时候初始化它们，而不是一次性使用全部。
### Reuse Temporary Memory Buffers 重用临时内存缓冲区
If you have a highly-used function that creates a large temporary buffer for some calculations, you might want to consider reusing that buffer rather than reallocating it each time you call the function. Even if your function needs a variable buffer space, you can always grow the buffer as needed using the `realloc` function. For multithreaded applications, the best way to reuse buffers is to add them to your thread-local storage. Although you could store the buffer using a static variable in your function, doing so would prevent you from using that function on multiple threads at the same time.

如果您有一个高频度使用的函数，函数里为一些计算创建一个大的临时缓冲区，您可能需要考虑重用该缓冲区，而不是每次调用该函数的时候重新分配它。即使您的函数需要一个可变缓冲区空间，您也可以随时使用`realloc`函数根据需要来增长缓冲区。对于多线程应用程序，重用缓冲区的最佳方法是将其添加到线程本地存储。虽然你可以在你的函数中使用一个静态变量来存储缓冲区，但这样做同时也会阻止您在多线程中使用那个函数。

Caching buffers eliminates much of the overhead for functions that regularly allocate and free large blocks of memory. However, this technique is only appropriate for functions that are called frequently. Also, you should be careful not to cache too many large buffers. Caching buffers does add to the memory footprint of your application and should only be used if testing indicates it would yield better performance.

缓存缓冲区消除了经常分配和释放大内存块的函数的开销。但是，这种技术仅适用于频繁调用的函数。另外，你应该小心不要缓存太多大的缓冲区。缓存缓冲区增加了应用的内存占用空间，只有在测试表明它会产生更好的性能时，才应该使用它。
### Free Unused Memory 释放未使用的内存
For memory allocated using the malloc library, it is important to free up memory as soon as you are done using it. Forgetting to free up memory can cause memory leaks, which reduces the amount of memory available to your application and impacts performance. Left unchecked, memory leaks can also put your application into a state where it cannot do anything because it cannot allocate the required memory.

对于使用malloc库分配的内存，一旦完成使用，尽快释放内存，这很重要。忘记释放内存会引起内存泄露，这会减少应用程序可用的内存量，并影响性能。不去管它的话，内存泄露也可以将您的应用程序置于无法执行任何操作的状态，因为它无法分配所需的内存。

>Note: Applications built using the Automatic Reference Counting (ARC) compiler option do not need to release Objective-C objects explicitly. Instead, the app must store strong references to objects it wants to keep and remove references to objects it does not need. When an object does not have any strong references to it, the compiler automatically releases it. For more information about supporting ARC, see [Transitioning to ARC Release Notes](https://developer.apple.com/library/content/releasenotes/ObjectiveC/RN-TransitioningToARC/Introduction/Introduction.html#//apple_ref/doc/uid/TP40011226).
>
>注意：使用自动引用计数(ARC)编译器选项构建的应用程序不需要显式释放Objective-C对象。相反，应用程序必须存储它想保持的对象的强引用，并移除它不再需要的对象的引用。当一个对象没有任何对它的强引用时，编译器会自动释放它。有关支持ARC的更多信息，请参阅[迁移到ARC发布说明](https://developer.apple.com/library/content/releasenotes/ObjectiveC/RN-TransitioningToARC/Introduction/Introduction.html#//apple_ref/doc/uid/TP40011226)

No matter which platform you are targeting, you should always eliminate memory leaks in your application. For code that uses malloc, remember that being lazy is fine for allocating memory but do not be lazy about freeing up that memory. To help track down memory leaks in your applications, use the Instruments app.

无论您定位哪个平台，你应该总是在你的应用程序中消除内存泄露。对于使用malloc的代码，请记住，懒分配内存是不错的，但不要懒释放内存。为了帮助追踪应用程序中的内存泄露，请使用Instruments应用。
## Memory Allocation Techniques 内存分配技术

Because memory is such a fundamental resource, OS X and iOS both provide several ways to allocate it. Which allocation techniques you use will depend mostly on your needs, but in the end all memory allocations eventually use the malloc library to create the memory. Even Cocoa objects are allocated using the malloc library eventually. The use of this single library makes it possible for the performance tools to report on all of the memory allocations in your application.

因为内存是这样一个基础资源，OS X和iOS都提供了几种分配方式。您使用哪些分配技术主要取决于您的需求，但最终所有的内存分配都将使用malloc库来创建内存。甚至Cocoa对象最终都是使用malloc库分配的。这个单一库的使用可以使性能分析工具报告应用程序中所有的内存分配。

If you are writing a Cocoa application, you might allocate memory only in the form of objects using the `alloc` method of `NSObject`. Even so, there may be times when you need to go beyond the basic object-related memory blocks and use other memory allocation techniques. For example, you might allocate memory directly using `malloc` in order to pass it to a low-level function call.

如果你正在编写一个Cocoa应用程序，开辟内存可能仅仅使用`NSObject`的`alloc`方法这种对象的形式。即使如此，有时您可能需要除基本对象相关的内存块之外，使用其他内存分配技术。例如，你可能直接使用`malloc`分配内存，以便将其传递到低级别的函数调用。

The following sections provide information about the malloc library and virtual memory system and how they perform allocations. The purpose of these sections is to help you identify the costs associated with each type of specialized allocation. You should use this information to optimize memory allocations in your 
code.

以下部分提供malloc库和虚拟内存系统的有关信息，以及他们是如何执行分配的。

>Note: These sections assume you are using the system supplied version of the malloc library to do your allocations. If you are using a custom malloc library, these techniques may not apply.
>
>注意：这些部分假设您正在使用系统提供的malloc库版本，如果您正在使用自定义的malloc库，这些技术可能不适用。这些部分的目的是帮助您识别每种特定分配方式的相关成本。您应该使用此信息来优化代码中的内存分配。

### Allocating Objects 分配对象
For Objective-C based applications, you allocate objects using one of two techniques. You can either use the `alloc` class method, followed by a call to a class initialization method, or you can use the `new` class method to allocate the object and call its default `init` method in one step.

对于基于Objective-C的应用程序来说，您使用两种技术之一来分配对象。您可以使用`alloc`类方法，紧随调用一个类初始化方法。或者您可以使用`new`类方法来分配对象，并在在一步中调用它默认的`init`方法。

After creating an object, the compiler’s ARC feature determines the lifespan of an object and when it should be deleted. Every new object needs at least one strong reference to it to prevent it from being deallocated right away. Therefore, when you create a new object, you should always create at least one strong reference to it. After that, you may create additional strong or weak references depending on the needs of your code. When all strong references to an object are removed, the compiler automatically deallocates it.

创建了一个对象之后，编译器的ARC特性将决定对象的生命周期以及何时它何时被删除。每一个新创建的对象都需要至少一个强引用，以防止它立即被释放。因此，当您创建一个新对象时，您应该至少创建一个对象的强引用。在那之后，您可以根据代码的需要来创建其它的强或弱引用。当对象所有的强引用都被删除时，编译器会自动释放它。

For more information about ARC and how you manage the lifespan of objects, see [Transitioning to ARC Release Notes](https://developer.apple.com/library/content/releasenotes/ObjectiveC/RN-TransitioningToARC/Introduction/Introduction.html#//apple_ref/doc/uid/TP40011226).

有关ARC的更多信息以及如何管理对象的生命周期，请参阅[迁移到ARC发行说明]()
### Allocating Small Memory Blocks Using Malloc 使用malloc分配小内存块
For small memory allocations, where small is anything less than a few virtual memory pages, `malloc` sub-allocates the requested amount of memory from a list (or “pool”) of free blocks of increasing size. Any small blocks you deallocate using the free routine are added back to the pool and reused on a “best fit” basis. The memory pool itself is comprised of several virtual memory pages that are allocated using the `vm_allocate` routine and managed for you by the system.

对于少于几个虚拟内存页的大小的小内存的分配，`malloc`从一个逐步增长尺寸的空闲块的列表(或“池”)中分配请求数量的内存。使用free例程释放分配的任何小块将添加回池中，并以"最合适"的方式重用。内存池本身由使用`vm_allocate`例程分配并由系统为您管理的多个虚拟内存页构成。

When allocating any small blocks of memory, remember that the granularity for blocks allocated by the malloc library is 16 bytes. Thus, the smallest block of memory you can allocate is 16 bytes and any blocks larger than that are a multiple of 16. For example, if you call `malloc` and ask for 4 bytes, it returns a block whose size is 16 bytes; if you request 24 bytes, it returns a block whose size is 32 bytes. Because of this granularity, you should design your data structures carefully and try to make them multiples of 16 bytes whenever possible.

当分配任何小块内存时，请记住，malloc库分配的块的粒度为16字节。因此，您可以分配的最小内存块为16字节，并且，任何大于那个数量的块都是16的倍数。例如，如果您调用`malloc`并且请求4个字节，则返回一个大小为16字节的块；如果您请求24字节，则返回一个大小为32字节的块。因为这种粒度，您应该仔细设计您的数据结构，并且尽可能使其成为16字节的倍数。

>Note: By their nature, allocations smaller than a single virtual memory page in size cannot be page aligned.
>
>注意：根据其性质，小于单个虚拟内存页大小的分配不能进行页面对齐。

### Allocating Large Memory Blocks using Malloc 使用Malloc分配大内存块
For large memory allocations, where large is anything more than a few virtual memory pages, `malloc` automatically uses the `vm_allocate` routine to obtain the requested memory. The `vm_allocate` routine assigns an address range to the new block in the logical address space of the current process, but it does not assign any physical memory to those pages right away. Instead, the kernel does the following:

对于任何大于几个虚拟内存页的大内存的分配，`malloc`自动使用`vm_allocate`例程来获取请求的内存。`vm_allocate`例程在当前进程的逻辑地址空间中指定一个地址范围到该新块。但不会立即为那些页面分配任何物理内存。相对应的，内存执行以下操作：

1. It maps a range of memory in the virtual address space of this process by creating a *map entry*; the map entry is a simple structure that defines the starting and ending addresses of the region.

	它通过创建一个映射条目(map entry)来在该进程的虚拟地址空间内映射一个内存范围。映射条目是一个简答的结构，定义了这个区域的起始和结束地址。

2. The range of memory is backed by the default pager.

	内存范围由默认分页器支持。

3. The kernel creates and initializes a VM object, associating it with the map entry.

	内核创建和初始化一个VM对象，将其与映射条目相关联。
	
At this point there are no pages resident in physical memory and no pages in the backing store. Everything is mapped virtually within the system. When your code accesses part of the memory block, by reading or writing to a specific address in it, a fault occurs because that address has not been mapped to physical memory. In OS X, the kernel also recognizes that the VM object has no backing store for the page on which this address occurs. The kernel then performs the following steps for each page fault:

在此刻，在物理内存中没有页面，并且在后备存储器中也没有页面。所有东西都在系统内被虚拟地映射。当你的代码该内存块的一部分时，通过读取或写入其中的某个特定地址，会发生故障，因为那个地址还没有被映射到物理内存。在OS X中，内核还可以识别在这个地址发生的页面没有后备存储器，然后，内核为每个页面故障执行以下几步：

1. It acquires a page from the free list and fills it with zeroes.

	它从空闲列表中获取一个页面，并将其填充为0。

2. It inserts a reference to this page in the VM object’s list of resident pages.

	它在VM对象的驻留页面列表中插入一个到这个页面的引用。

3. It maps the virtual page to the physical page by filling in a data structure called the *pmap*. The pmap contains the page table used by the processor (or by a separate memory management unit) to map a given virtual address to the actual hardware address.

	它通过填充一个叫做*pmap*的数据结构来映射该虚拟页面到物理页。该pmap包含被处理器(或单独的内存管理单元)使用的页表，以将给定的虚拟地址映射到实际的硬件地址。
	
The granularity of large memory blocks is equal to the size of a virtual memory page, or 4096 bytes. In other words, any large memory allocations that are not a multiple of 4096 are rounded up to this multiple automatically. Thus, if you are allocating large memory buffers, you should make your buffer a multiple of this size to avoid wasting memory.

大内存块的粒度跟虚拟内存页的大小相等，或4096字节。换句话说，不是4096倍数的任何大的内存分配将自动向上舍入到该倍数。因此，如果你正在你分配大内存缓冲区，你应该将缓冲区设置为这个大小的倍数，以避免浪费内存。
> Note: Large memory allocations are guaranteed to be page-aligned.
> 
> 注意：大内存分配是保证页对齐的。

For large allocations, you may also find that it makes sense to allocate virtual memory directly using `vm_allocate`, rather than using `malloc`. The example in Listing 2 shows how to use the `vm_allocate` function.

对于大的分配，您可能还会发现使用`vm_allocate`直接分配虚拟内存是有意义的，而不是使用`malloc`。列表2中的示例显示了如何使用`vm_allocate`函数

**Listing 2**  Allocating memory with vm_allocate

**列表2** 使用vm_allocate分配内存

```
void* AllocateVirtualMemory(size_t size)
{
    char*          data;
    kern_return_t   err;
 
    // In debug builds, check that we have
    // correct VM page alignment
    check(size != 0);
    check((size % 4096) == 0);
 
    // Allocate directly from VM
    err = vm_allocate(  (vm_map_t) mach_task_self(),
                        (vm_address_t*) &data,
                        size,
                        VM_FLAGS_ANYWHERE);
 
    // Check errors
    check(err == KERN_SUCCESS);
    if(err != KERN_SUCCESS)
    {
        data = NULL;
    }
 
    return data;
}
```
### Allocating Memory in Batches 批量分配内存
If your code allocates multiple, identically-sized memory blocks, you can use the `malloc_zone_batch_malloc` function to allocate those blocks all at once. This function offers better performance than a series of calls to `malloc` to allocate the same memory. Performance is best when the individual block size is relatively small—less than 4K in size. The function does its best to allocate all of the requested memory but may return less than was requested. When using this function, check the return values carefully to see how many blocks were actually allocated.

如果你的代码分配多个相同大小的内存块，你可以使用`malloc_zone_batch_malloc`函数一次性分配那些块。这个函数比一系列`malloc`的调用来分配同样的内存来说，提供了更好的性能。当单个块大小相对小于4K时，性能最好。该函数尽力分配所有请求的内存，但是可能返回小于请求的内存。当使用这个函数的时候，请仔细检查返回值，以查看实际上分配了多少块。

Batch allocation of memory blocks is supported in OS X version 10.3 and later and in iOS. For information, see the `/usr/include/malloc/malloc.h` header file.

在OS X 10.3及更高版本和iOS中，批量分配内存是被支持的。有关信息，请参阅`/usr/include/malloc/malloc.h`文件。

### Allocating Shared Memory 分配共享内存
Shared memory is memory that can be written to or read from by two or more processes. Shared memory can be inherited from a parent process, created by a shared memory server, or explicitly created by an application for export to other applications. Uses for shared memory include the following:

共享内存是可以通过两个或更多进程写入或读取的内存。共享内存可以从父进程继承，由一个共享服务创建，或被一个应用程序显式地创建以输出到其他应用程序，使用共享内存包括以下方面:

* Sharing large resources such as icons or sounds
  
  共享大量资源，比如图标或声音	
 
* Fast communication between one or more processes

  一个或多个进程的快速通信
  
Shared memory is fragile and is generally not recommended when other, more reliable alternatives are available. If one program corrupts a section of shared memory, any programs that also use that memory share the corrupted data. The functions used to create and manage shared memory regions are in the `/usr/include/sys/shm.h` header file.

共享内存很脆弱，且通常在有其它可用办法的时候不建议使用。如果一个程序破坏了共享内存的一个区域，任何其他也用那个内存的程序可能访问了被破坏的数据。创建和管理共享内存区域的函数使用，在`usr/include/sys/shm.h`头文件中。
### Using Malloc Memory Zones 使用Malloc内存区域

All memory blocks are allocated within a malloc zone (also referred to as a malloc heap). A zone is a variable-size range of virtual memory from which the memory system can allocate blocks. A zone has its own free list and pool of memory pages, and memory allocated within the zone remains on that set of pages. Zones are useful in situations where you need to create blocks of memory with similar access patterns or lifetimes. You can allocate many objects or blocks of memory in a zone and then destroy the zone to free them all, rather than releasing each block individually. In theory, using a zone in this way can minimize wasted space and reduce paging activity. In reality, the overhead of zones often eliminates the performance advantages associated with the zone.

所有的内存块都被分配在一个malloc区域(也称为malloc堆)中。一个区域(zone)是虚拟内存的可变大小的范围，内存系统可次用该区域分配块。一个区域有它自己内存页的空闲列表和内存页池。在该区域内分配的内存保留在那个内存页池中。在你需要创建的具有相似访问模式或生命周期的内存块的情况下，区域是非常有用的。你可以在一个区域中分配许多对象或内存块，然后销毁该区域以释放所有对象，而不是单独释放每个块。理论上说，以这种方式使用区域可以最大限度地减少浪费空间，并减少分页操作。实际上，区域的开销通常会消除与该区域相关的性能优势。

> **Note:** The term zone is synonymous with the terms heap, pool, and arena in terms of memory allocation using the `malloc` routines.
> 
> **注意:** 在使用`malloc`例程的内存分配术语中，区域(zone)这个术语跟堆(heap)、池(pool)，和舞台(arena)术语是同义的。

By default, allocations made using the `malloc` function occur within the default malloc zone, which is created when `malloc` is first called by your application. Although it is generally not recommended, you can create additional zones if measurements show there to be potential performance gains in your code. For example, if the effect of releasing a large number of temporary (and isolated) objects is slowing down your application, you could allocate them in a zone instead and simply deallocate the zone.

默认情况下，使用`malloc`函数进行的分配发生在默认的malloc区域，该区域在您应用程序第一次调用`malloc`时创建。虽然通常不推荐，但如果测试结果显示您的代码具有潜在的性能提升，则可以创建额外的区域。例如，如果释放大量临时(和隔离)对象的影响正在减慢您应用程序的速度，你可以把他们分配在一个区域，并且只是简单地释放该区域。

If you are create objects (or allocate memory blocks) in a custom malloc zone, you can simply free the entire zone when you are done with it, instead of releasing the zone-allocated objects or memory blocks individually. When doing so, be sure your application data structures do not hold references to the memory in the custom zone. Attempting to access memory in a deallocated zone will cause a memory fault and crash your application.

如果您创建对象(或分配内存块)在一个自定义的malloc区域，您可以在完成操作后简单地释放整个区域，而不是单独释放区域分配的对象或内存块。当这样做时，确保您应用程序的数据结构不保留对自定义区域内存的引用。尝试访问已释放区域的内存，将会引起一个内存错误，并使应用程序崩溃。

> **Warning:** You should never deallocate the default zone for your application.
> 
> **警告:**您应该永远不要为应用程序释放默认区域。

At the malloc library level, support for zones is defined in `/usr/include/malloc/malloc.h`. Use the `malloc_create_zone` function to create a custom malloc zone or the `malloc_default_zone` function to get the default zone for your application. To allocate memory in a particular zone, use the `malloc_zone_malloc` , `malloc_zone_calloc` , `malloc_zone_valloc` , or `malloc_zone_realloc` functions. To release the memory in a custom zone, call `malloc_destroy_zone`.

在malloc库级别，对区域的支持定义在`/usr/include/malloc/malloc.h`。使用`malloc_create_zone`函数创建一个自定义的malloc区域，或使用`malloc_default_zone`函数获得您应用程序的默认区域。要在特定区域分配内存，使用`malloc_zone_malloc` , `malloc_zone_calloc` , `malloc_zone_valloc` , 或 `malloc_zone_realloc`函数。要再一个自定义区域释放内存，调用`malloc_destroy_zone`。
## Copying Memory Using Malloc 使用Malloc复制内存
There are two main approaches to copying memory in OS X: direct and delayed. For most situations, the direct approach offers the best overall performance. However, there are times when using a delayed-copy operation has its benefits. The goal of the following sections is to introduce you to the different approaches for copying memory and the situations when you might use those approaches.

在OS X中复制内存有两种主要的方法：直接和延迟。在大多数情况下，直接的方法提供了最佳的整体性能。然而，有时使用延迟复制操作有其好处。以下部分的目标是向您介绍复制内存的不同方法以及可能使用这些方法的场合。
### Copying Memory Directly 直接复制内存
The direct copying of memory involves using a routine such as `memcpy` or `memmove` to copy bytes from one block to another. Both the source and destination blocks must be resident in memory at the time of the copy. However, these routines are especially suited for the following situations:

直接的内存复制涉及诸如`memcpy` 或 `memmove`之类的例程将字节从一个快复制到另一个块。在复制时，源和目标块都必须驻留在内存中。然而，这些例程特别适用于以下情况：

* The size of the block you want to copy is small (under 16 kilobytes).

	你想复制的块的大小是比较小的(小于16KB)。

* You intend to use either the source or destination right away.

	您打算立即使用源或目的地内存。
* The source or destination block is not page aligned.

	源或目的地块不是页对齐的。
* The source and destination blocks overlap.

	源和目的地块部分重叠。

If you do not plan to use the source or destination data for some time, performing a direct copy can decrease performance significantly for large memory blocks. Copying the memory directly increases the size of your application’s working set. Whenever you increase your application’s working set, you increase the chances of paging to disk. If you have two direct copies of a large memory block in your working set, you might end up paging them both to disk. When you later access either the source or destination, you would then need to load that data back from disk, which is much more expensive than using `vm_copy` to perform a delayed copy operation.

如果您在一段时间内并不打算使用源或目标数据，执行直接复制可能会大幅降低大内存块的性能。直接复制内存增加了应用程序工作集的大小。无论您何时增加您应用程序的工作集的大小，您增加了分页到磁盘的机会。如果您工作集中有两个大内存块的直接副本，您可能最终将它们分页到磁盘。当您以后访问源或目的地时，您将需要从磁盘加载该数据，这比`vm_copy`执行延迟复制要昂贵得多。

> **Note:** If the source and destination blocks overlap, you should prefer the use of `memmove` over `memcpy`. The implementation of `memmove` handles overlapping blocks correctly in OS X, but the implementation of `memcpy` is not guaranteed to do so.
> 
> **注意:**如果源和目标块部分重叠，您应优先使用`memmove`，而不是`memcpy`。`memmove`的实现在OS X中正确处理了重叠块，但是`memcpy`的实现是不被保证做了同样的事情的。

### Delaying Memory Copy Operations 延迟内存复制操作
If you intend to copy many pages worth of memory, but don’t intend to use either the source or destination pages immediately, then you may want to use the `vm_copy` function to do so. Unlike `memmove` or `memcpy`, `vm_copy` does not touch any real memory. It modifies the virtual memory map to indicate that the destination address range is a copy-on-write version of the source address range.

如果您打算复制许多页面的内存，但是不打算立即使用源或目的页面。则您可能需要使用`vm_copy`函数来这样做。不像`memmove` 或 `memcpy`, `vm_copy`不触及任何真正的内存。它修改虚拟内存映射，以指示目标地址范围是源地址范围的写时复制版本。

The `vm_copy` routine is more efficient than `memcpy` in very specific situations. Specifically, it is more efficient in cases where your code does not access either the source or destination memory for a fairly large period of time after the copy operation. The reason that `vm_copy` is effective for delayed usage is the way the kernel handles the copy-on-write case. In order to perform the copy operation, the kernel must remove all references to the source pages from the virtual memory system. The next time a process accesses data on that source page, a soft fault occurs, and the kernel maps the page back into the process space as a copy-on-write page. The process of handling a single soft fault is almost as expensive as copying the data directly.

在个别情况下，该`vm_copy`例程比`memcpy`更有效。具体来说，在您的代码在复制操作之后相当长的一段时间内不访问源或目的内存的情况下，它的效率更高。`vm_copy`对延迟使用更有效率的原因是内核处理写时复制情况的方式。为了执行复制操作，内核必须从虚拟内存系统中移除对源页面的所有引用。下一次进程访问该源页面上的数据时，会发生软故障，内核将页面作为写时复制页面映射回进程空间。处理单个软故障的过程与直接复制数据几乎一样昂贵。 

### Copying Small Amounts of Data 复制少量数据
If you need to copy a small blocks of non-overlapping data, you should prefer `memcpy` over any other routines. For small blocks of memory, the GCC compiler can optimize this routine by replacing it with inline instructions to copy the data by value. The compiler may not optimize out other routines such as `memmove` or `BlockMoveData`.

如果您需要复制一小块非重叠数据的块，您应该优先使用`memcpy`，而不是任何其它例程。对于小的内存块，GCC编译器可以通过inline指令替换它来对数据进行复制来优化此例程。编译器可能无法优化其它例程，例如`memmove` 或 `BlockMoveData`。

### Copying Data to Video RAM 复制数据到视频RAM
When copying data into VRAM, use the `BlockMoveDataUncached` function instead of functions such as `bcopy`. The `bcopy` function uses cache-manipulation instructions that may cause exception errors. The kernel must fix these errors in order to continue, which slows down performance tremendously.

当复制数据到VRAM中时，使用`BlockMoveDataUncached`函数，而不是如`bcopy`这样的函数。`bcopy`函数使用可能引起异常错误的缓存处理指令。内核必须修复这些错误才能继续，这会大大降低性能。

## Responding to Low-Memory Warnings in iOS 在iOS中响应低内存警告

The virtual memory system in iOS does not use a backing store and instead relies on the cooperation of applications to remove strong references to objects. When the number of free pages dips below the computed threshold, the system releases unmodified pages whenever possible but may also send the currently running application a low-memory notification. If your application receives this notification, heed the warning. Upon receiving it, your application must remove strong references to as many objects as possible. For example, you can use the warnings to clear out data caches that can be recreated later.

在iOS中的虚拟内存系统不使用后备存储器，而是依赖应用程序的合作来移除对象的强引用。当空闲页的数量低于计算的阈值时，任何可能的时候，系统会释放未修改的页面，也可能会
发送一个低内存通知给当前正在运行的应用程序。如果你的应用程序接收到这个通知，请注意警告。一旦接收到它，你的应用程序必须尽可能多地移除对象的强引用。例如，你可以使用警告来移除稍后可以重新创建的数据缓存。

UIKit provides several ways to receive low-memory notifications, including the following:

UIKit提供了几种接收低内存通知的方法，其中包括：

* Implement the [applicationDidReceiveMemoryWarning:](https://developer.apple.com/documentation/uikit/uiapplicationdelegate/1623063-applicationdidreceivememorywarni) method of your application delegate.

	实现您应用程序代理的[applicationDidReceiveMemoryWarning:](https://developer.apple.com/documentation/uikit/uiapplicationdelegate/1623063-applicationdidreceivememorywarni)方法。

* Override the [didReceiveMemoryWarning](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621409-didreceivememorywarning) method in your custom `UIViewController` subclass.
	
	在你自定义的`UIViewController`子类中重写[didReceiveMemoryWarning](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621409-didreceivememorywarning)方法。

* Register to receive the [UIApplicationDidReceiveMemoryWarningNotification](https://developer.apple.com/documentation/uikit/uiapplicationdidreceivememorywarningnotification) notification.

	注册接收[UIApplicationDidReceiveMemoryWarningNotification](https://developer.apple.com/documentation/uikit/uiapplicationdidreceivememorywarningnotification)通知。

Upon receiving any of these notifications, your handler method should respond by immediately removing strong references to objects. View controllers automatically remove references to views that are currently offscreen, but you should also override the [didReceiveMemoryWarning](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621409-didreceivememorywarning) method and use it to remove any additional references that your view controller does not need.

一旦接受到任何这些通知，您的处理方法应该通过立即移除对象的强引用来响应。视图控制器自动移除了当前离屏视图的引用，但您应该重写[didReceiveMemoryWarning](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621409-didreceivememorywarning)方法，并使用它来移除任何您视图控制器不需要的额外的引用。

If you have only a few custom objects with known purgeable resources, you can have those objects register for the `UIApplicationDidReceiveMemoryWarningNotification` notification and remove references there. If you have many purgeable objects or want to selectively purge only some objects, however, you might want to use your application delegate to decide which objects to keep.

如果您只有几个具有已知可清除资源的自定义对象，你可以让那些对象注册`UIApplicationDidReceiveMemoryWarningNotification`通知，并且在那里移除引用。如果您有许多可清除对象或仅仅想要选择性的清除一些对象，则您可能想要使用您应用程序的代理来决定保留哪些对象。

> **Important:** Like the system applications, your applications should always handle low-memory warnings, even if they do not receive those warnings during your testing. System applications consume small amounts of memory while processing requests. When a low-memory condition is detected, the system delivers low-memory warnings to all running programs (including your application) and may terminate some background applications (if necessary) to ease memory pressure. If not enough memory is released—perhaps because your application is leaking or still consuming too much memory—the system may still terminate your application.
> 
> **重要：**与系统应用程序一样，您的应用程序应该始终处理低内存警告，即使在您的测试期间它们并没有收到这些警告。系统应用程序在处理请求时消耗少量内存。当检测到低内存条件时，系统会向所有正在运行的程序(包括你的应用程序)传递低内存警告，并可能终止一些后台程序(如有必要)以缓解内存压力。如果没有足够的内存被释放-可能因为您的应用程序内存泄露或仍然消耗大量内存-系统仍可能会终止您的应用程序。