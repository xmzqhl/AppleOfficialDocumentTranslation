# Tips for Allocating Memory 分配内存的技巧
Memory is an important resource for your application so it’s important to think about how your application will use memory and what might be the most efficient allocation approaches. Most applications do not need to do anything special; they can simply allocate objects or memory blocks as needed and not see any performance degradation. For applications that use large amount of memory, however, carefully planning out your memory allocation strategy could make a big difference.

内存是您应用程序的重要资源，因此，考虑您的应用程序将如何使用内存和最有效的分配方法是什么，这是比较重要的。大多数应用程序不需要做任何特殊的操作；它们可以根据需要简单地分配对象或内存块，而不会看到任何性能的下降。然后，对于使用大量内存的应用程序来说，仔细规划您的内存分配策略可能会产生很大的影响。

The following sections describe the basic options for allocating memory along with tips for doing so efficiently. To determine if your application has memory performance problems in the first place, you need to use the Xcode tools to look at your application’s allocation patterns while it is running. For information on how to do that, see [Tracking Memory Usage](https://developer.apple.com/library/content/documentation/Performance/Conceptual/ManagingMemory/Articles/FindingPatterns.html#//apple_ref/doc/uid/20001882-CJBJFIDD).

以下部分描述了分配内存的基本选项以及高效分配内存的技巧。要确定您的应用程序是否有内存性能问题，首先，在您应用运行的时候，需要使用Xcode工具来查看它的分配模式。有关如何执行此操作的信息，请参阅[跟踪内存使用](Tracking Memory Usage.md)
## Tips for Improving Memory-Related Performance 提高内存相关性能的技巧
As you design your code, you should always be aware of how you are using memory. Because memory is an important resource, you want to be sure to use it efficiently and not be wasteful. Besides allocating the right amount of memory for a given operation, the following sections describe other ways to improve the efficiency of your program’s memory usage.

在设计你的代码时，您应该始终了解如何使用内存。因为内存是一个重要的资源，所以你要确保高效使用它和不浪费。除了为给定的操作分配适量的内存之外，以下部分介绍了其他方法来提供内存使用的效率。
## Defer Your Memory Allocations 延迟你的内存分配
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
## Initialize Memory Blocks Efficiently 有效的初始化内存块
Small blocks of memory, allocated using the `malloc` function, are not guaranteed to be initialized with zeroes. Although you could use the `memset` function to initialize the memory, a better choice is to use the `calloc` routine to allocate the memory in the first place. The `calloc` function reserves the required virtual address space for the memory but waits until the memory is actually used before initializing it. This approach is much more efficient than using `memset`, which forces the virtual memory system to map the corresponding pages into physical memory in order to zero-initialize them. Another advantage of using the `calloc` function is that it lets the system initialize pages as they’re used, as opposed to all at once.

使用`malloc`函数分配的小块内存，不被保证会用0初始化。尽管你可以使用`memset `函数来初始化内存，但更好的选择是使用`calloc`例程分配内存。`calloc`函数储存内存所需的虚拟地址空间，但是，一直等待直到该内存在初始化它之前实际被使用的时候。这种方法比使用`memset`更有效率，这迫使虚拟内存系统将对应的页面映射到物理内存中，以便对其进行0初始化。使用`calloc`函数的另一个好处是，它让系统在页面被使用的时候初始化它们，而不是一次性使用全部。
## Reuse Temporary Memory Buffers 重用临时内存缓冲区
If you have a highly-used function that creates a large temporary buffer for some calculations, you might want to consider reusing that buffer rather than reallocating it each time you call the function. Even if your function needs a variable buffer space, you can always grow the buffer as needed using the `realloc` function. For multithreaded applications, the best way to reuse buffers is to add them to your thread-local storage. Although you could store the buffer using a static variable in your function, doing so would prevent you from using that function on multiple threads at the same time.

如果您有一个高频度使用的函数，函数里为一些计算创建一个大的临时缓冲区，您可能需要考虑重用该缓冲区，而不是每次调用该函数的时候重新分配它。即使您的函数需要一个可变缓冲区空间，您也可以随时使用`realloc`函数根据需要来增长缓冲区。对于多线程应用程序，重用缓冲区的最佳方法是将其添加到线程本地存储。虽然你可以在你的函数中使用一个静态变量来存储缓冲区，但这样做同时也会阻止您在多线程中使用那个函数。

Caching buffers eliminates much of the overhead for functions that regularly allocate and free large blocks of memory. However, this technique is only appropriate for functions that are called frequently. Also, you should be careful not to cache too many large buffers. Caching buffers does add to the memory footprint of your application and should only be used if testing indicates it would yield better performance.

缓存缓冲区消除了经常分配和释放大内存块的函数的开销。但是，这种技术仅适用于频繁调用的函数。另外，你应该小心不要缓存太多大的缓冲区。缓存缓冲区增加了应用的内存占用空间，只有在测试表明它会产生更好的性能时，才应该使用它。
## Free Unused Memory 释放未使用的内存
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

##Allocating Objects 分配对象
For Objective-C based applications, you allocate objects using one of two techniques. You can either use the `alloc` class method, followed by a call to a class initialization method, or you can use the `new` class method to allocate the object and call its default `init` method in one step.

对于基于Objective-C的应用程序来说，您使用两种技术之一来分配对象。您可以使用`alloc`类方法，紧随调用一个类初始化方法。或者您可以使用`new`类方法来分配对象，并在在一步中调用它默认的`init`方法。

After creating an object, the compiler’s ARC feature determines the lifespan of an object and when it should be deleted. Every new object needs at least one strong reference to it to prevent it from being deallocated right away. Therefore, when you create a new object, you should always create at least one strong reference to it. After that, you may create additional strong or weak references depending on the needs of your code. When all strong references to an object are removed, the compiler automatically deallocates it.

创建了一个对象之后，编译器的ARC特性将决定对象的生命周期以及何时它何时被删除。每一个新创建的对象都需要至少一个强引用，以防止它立即被释放。因此，当您创建一个新对象时，您应该至少创建一个对象的强引用。在那之后，您可以根据代码的需要来创建其它的强或弱引用。当对象所有的强引用都被删除时，编译器会自动释放它。

For more information about ARC and how you manage the lifespan of objects, see [Transitioning to ARC Release Notes](https://developer.apple.com/library/content/releasenotes/ObjectiveC/RN-TransitioningToARC/Introduction/Introduction.html#//apple_ref/doc/uid/TP40011226).

有关ARC的更多信息以及如何管理对象的生命周期，请参阅[迁移到ARC发行说明]()
## Allocating Small Memory Blocks Using Malloc 使用malloc分配小内存块
For small memory allocations, where small is anything less than a few virtual memory pages, malloc sub-allocates the requested amount of memory from a list (or “pool”) of free blocks of increasing size. Any small blocks you deallocate using the free routine are added back to the pool and reused on a “best fit” basis. The memory pool itself is comprised of several virtual memory pages that are allocated using the vm_allocate routine and managed for you by the system.

When allocating any small blocks of memory, remember that the granularity for blocks allocated by the malloc library is 16 bytes. Thus, the smallest block of memory you can allocate is 16 bytes and any blocks larger than that are a multiple of 16. For example, if you call malloc and ask for 4 bytes, it returns a block whose size is 16 bytes; if you request 24 bytes, it returns a block whose size is 32 bytes. Because of this granularity, you should design your data structures carefully and try to make them multiples of 16 bytes whenever possible.
>Note: By their nature, allocations smaller than a single virtual memory page in size cannot be page aligned.
>
>注意：根据其性质，小于单个虚拟内存页大小的分配不能进行页面对齐。