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

Caching buffers eliminates much of the overhead for functions that regularly allocate and free large blocks of memory. However, this technique is only appropriate for functions that are called frequently. Also, you should be careful not to cache too many large buffers. Caching buffers does add to the memory footprint of your application and should only be used if testing indicates it would yield better performance.

