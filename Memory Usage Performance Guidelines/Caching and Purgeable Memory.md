# Caching and Purgeable Memory 缓存和可清除内存
Caching and purgeable memory can be vital assets to developers who are dealing with large objects that require significant memory or computation time, or developers whose code is getting bogged down as the computer writes data to disk because the RAM is full.

缓存和可清除内存可能是处理需要大量内存或计算时间的大型对象，或者因为RAM已满，计算机写入数据到磁盘时，代码陷入困境的开发人员的重要资产，

## Overview of Caching 缓存概述

A cache is a collection of objects or data that can greatly increase the performance of applications.

缓存是可以大量提高应用程序性能的对象或数据的集合。

### Why Use Caching? 为什么使用缓存？
Developers use caches to store frequently accessed objects with transient data that can be expensive to compute. Reusing these objects can provide performance benefits, because their values do not have to be recalculated. However, the objects are not critical to the application and can be discarded if memory is tight. If discarded, their values will have to be recomputed again when needed.

开发者使用缓存来存储频繁访问的对象，这些对象具有可能花费昂贵时间计算的临时数据。重用这些对象可以提供性能优势，因为他们的值不需要重新计算。但是这些对象对应用程序来说不重要，如果内存不足，可能会丢弃该对象。如果丢弃，当需要时必须重新计算他们的值。

### Problems Caching Can Cause 缓存可能引起的问题
Although caching can provide enormous benefits in terms of performance, there are also some possible drawbacks that caching presents. Most importantly, caching can use very large amounts of memory. When caching many large data objects, it is possible to cache so many objects that there is no RAM left for other applications, and the computer will grind to a halt as it writes all of this data to disk in order to free up RAM.

尽管缓存可以在性能方面提供巨大的好处，但缓存还可能有一些缺点。最重要的是，缓存可以使用非常大的内存。当缓存许多大数据对象时，可以缓存这么多对象使其他程序没有可用的RAM，并且，当它将这些数据写入到磁盘以释放内存时，计算机将停止工作。

### Solutions 解决方案
Cocoa provides an [NSCache](https://developer.apple.com/documentation/foundation/nscache) object as a convenient storage container for items you want to cache, while at the same time addressing the memory management issues discussed above. The `NSCache` class is very similar to the [NSDictionary](https://developer.apple.com/documentation/foundation/nsdictionary) class, in that they both hold key-value pairs. However, an `NSCache` object is a “reactive cache.” That is, when memory is available, it aggressively caches any data it is given. Yet, when memory is low, it will automatically discard some of its elements in order to free up memory for other applications. Later, if these discarded items are needed, their values will have to be recalculated.

Cocoa提供一个[NSCache](https://developer.apple.com/documentation/foundation/nscache)对象作为缓存的项目的便捷存储容器，同时解决上述内存管理问题。该`NSCache`类和 [NSDictionary](https://developer.apple.com/documentation/foundation/nsdictionary)类非常相似，因为他们都保存一个键值对。然而，一个`NSCache`对象是一个“响应式缓存(reactive cache)”。也就是说，当内存可用时，它会积极地缓存任何给定的数据。然而，当内存不足时，它会自动丢弃其中的一些元素，以便为其他应用程序释放内存。之后，如果需要这些丢弃的条目，则必须重新计算其值。

`NSCache` provides two other useful "limit" features: limiting the number of cached elements and limiting the total cost of all elements in the cache. To limit the number of elements that the cache is allowed to have, call the method [setCountLimit:](https://developer.apple.com/documentation/foundation/nscache/1416355-countlimit). For example, if you try to add 11 items to a cache whose [countLimit](https://developer.apple.com/documentation/foundation/nscache/1416355-countlimit) is set to `10`, the cache could automatically discard one of the elements.

`NSCache`提供了另外两个有用的“限制”功能：限制缓存元素的数量和限制缓存中所有元素的总成本。要限制缓存允许的元素数量，请调用[setCountLimit:](https://developer.apple.com/documentation/foundation/nscache/1416355-countlimit)方法。例如，如果您尝试将11个条目添加到一个[countLimit](https://developer.apple.com/documentation/foundation/nscache/1416355-countlimit)设置为`10`的缓存，那么，缓存可能自动丢弃其中一个元素。

When adding items to a cache, you can specify a `cost` value to be associated with each key-value pair. Call the [setTotalCostLimit:](https://developer.apple.com/documentation/foundation/nscache/1407672-totalcostlimit) method to set the maximum value for the sum of all the cached objects’ costs. Thus, when an object is added that pushes the `totalCost` above the `totalCostLimit`, the cache could automatically evict some of its objects in order to get back below the threshold. This eviction process is not guaranteed, so trying to manipulate the `cost` values to achieve specific behavior could be detrimental to the performance of the cache. Pass in `0` for the `cost` if you have nothing useful, or use the [setObject:forKey:](https://developer.apple.com/documentation/foundation/nscache/1408223-setobject) method, which does not require a cost to be passed in.

将添加条目到一个缓存时，你可以指定一个`cost`值与每个键值对相关联。调用[setTotalCostLimit:](https://developer.apple.com/documentation/foundation/nscache/1407672-totalcostlimit)方法来设置所有缓存对象成本总和的最大值。因此，当一个对象被添加后，推动`totalCost`大于`totalCostLimit`，缓存可以自动地驱逐其一些对象，以便回到低于阈值。这种驱逐过程是不能保证的，所以尝试操纵这个`cost`值来实现特定的行为，可能会对缓存的性能造成不利影响。如果你没有什么用处,为`cost`传入`0`，或者使用 [setObject:forKey:](https://developer.apple.com/documentation/foundation/nscache/1408223-setobject)方法，它不需要传递成本。

> **Note:** The count limit and the total-cost limit are not strictly enforced. That is, when the cache goes over one of its limits, some of its objects might get evicted immediately, later, or never, all depending on the implementation details of the cache.
> 
> **注意：** 数量限制和总成本限制不被严格执行。也就是说，当缓存超过其中一个限制时，其一些对象可能会立即，稍后或从不被驱逐，这取决于缓存的实现细节。

## Using Purgeable Memory 使用可清除内存
The Cocoa framework also provides the [NSPurgeableData](https://developer.apple.com/documentation/foundation/nspurgeabledata) class to help ensure that your applications do not use up too much memory. The `NSPurgeableData` class adopts the [NSDiscardableContent](https://developer.apple.com/documentation/foundation/nsdiscardablecontent) protocol, which any class can implement to allow memory to be discarded when clients of the class's instances are finished accessing those objects. You should implement `NSDiscardableContent` when creating objects that have disposable subcomponents. In addition, the `NSPurgeableData` class does not have to be used in conjunction with `NSCache`; you may use it independently to get purging behavior.

Cocoa框架还提供了[NSPurgeableData](https://developer.apple.com/documentation/foundation/nspurgeabledata)类，以帮助确保您的应用程序不会占用太多的内存。`NSPurgeableData`类采用[NSDiscardableContent](https://developer.apple.com/documentation/foundation/nsdiscardablecontent)协议，任何类都可以实现，以允许当所述类的实例的客户端完成了访问那些对象时，允许内存被丢弃。当创建具有一次性子组件的对象时，你应该实现`NSDiscardableContent`。此外，`NSPurgeableData`类不必与`NSCache`配合使用; 您可以独立使用它来获得可清除行为。

### Advantages of Using Purgeable Memory 使用可清除内存的优点
By using purgeable memory, you allow the system to quickly recover memory if it needs to, thereby increasing performance. Memory that is marked as purgeable is not paged to disk when it is reclaimed by the virtual memory system because paging is a time-consuming process. Instead, the data is discarded, and if needed later, it will have to be recomputed.

通过使用可清除内存，您可以允许系统在需要时快速恢复内存，从而提高性能。当被虚拟内存系统回收时，标记为可清除的内存不会被分页到磁盘，因为分页是一个耗时的过程。相反，数据被丢弃，并且如果以后需要，它将被重新计算。

A caveat when using purgeable memory is that the block of memory must be locked before being accessed. This locking mechanism is necessary to ensure that no auto-removal policies try to dispose of the data while you are accessing it. Similarly, the locking mechanism will ensure that the virtual memory system has not already discarded the data. The `NSPurgeableData` class implements a very simple locking mechanism to ensure that the data is safe while it is 
being read.

使用可清除内存时需要注意的是，在访问之前必须锁内存块。这种锁定机制是必要的，以确保在访问数据时没有自动删除策略尝试处理数据。类似地，锁定机制将确保虚拟内存系统尚未丢弃数据。`NSPurgeableData`类实现了一个非常简单的锁定机制，以确保数据在读取时是安全的。

### How to Implement Purgeable Memory 如何实现可清除内存
The `NSPurgeableData` class is very simple to use, because the class simply implements the `NSDiscardableContent` protocol. The notion of a “counter” variable is central to the life cycle of `NSDiscardableContent` objects. When the memory being used by this object is being read, or is still needed, its counter variable will be greater than or equal to `1`. When it is not being used, and can be discarded, the counter variable is equal to `0`.

`NSPurgeableData`类使用起来非常简单，因为该类只是实现了`NSDiscardableContent`协议。“counter”变量的概念是`NSDiscardableContent`对象生命周期的核心。当该对象被读取导致内存被使用或内存仍然被需要时，其计数器变量将大于或等于`1`。当它不被使用，并且可以被丢弃时，计数器变量等于`0`。

When the counter is equal to 0, the block of memory may be discarded if memory is tight. To discard the content, call [discardContentIfPossible](https://developer.apple.com/documentation/foundation/nsdiscardablecontent/1408998-discardcontentifpossible) on the object, which frees the associated memory if the counter variable equals `0`.

当计数器等于0时，如果内存紧张，则内存块可能被丢弃。要丢弃内容，请在这个对象上调用[discardContentIfPossible](https://developer.apple.com/documentation/foundation/nsdiscardablecontent/1408998-discardcontentifpossible)，如果计数器变量等于`0`，则会释放相关的内存。

By default, when an `NSPurgeableData` object is initialized, it is created with the counter variable equal to `1` and can safely be accessed. To access purgeable memory, simply call the [beginContentAccess](https://developer.apple.com/documentation/foundation/nsdiscardablecontent/1412187-begincontentaccess) method. This method will first check to make sure the object’s data has not been discarded. If the data is still there, it will increment the counter variable in order to protect the memory while it is being read, and return `YES`. If the data has been discarded, this method will return `NO`. When you are done accessing the data, call [endContentAccess](https://developer.apple.com/documentation/foundation/nsdiscardablecontent/1407535-endcontentaccess), which decrements the counter and allows the memory to be discarded if the system desires to do so. You must keep track of the counter variable’s state and access memory only if the `beginContentAccess` method returns `YES`.

默认情况下，当一个`NSPurgeableData`对象被初始化时，它被创建，并且它的计数器变量等于`1`，并可以安全地被访问。要访问可清除内存，只需调用该[beginContentAccess](https://developer.apple.com/documentation/foundation/nsdiscardablecontent/1412187-begincontentaccess)方法。此方法将首先检查以确保对象的数据未被丢弃。如果数据仍然在那，它将递增计数器变量，以便在读取时保护这个内存，并返回`YES`。如果数据已被丢弃，此方法返回`NO`。当您完成对数据的访问后，调用[endContentAccess](https://developer.apple.com/documentation/foundation/nsdiscardablecontent/1407535-endcontentaccess)，此方法递减计数器，并且如果系统希望的话，允许该内存被丢弃。你必须跟踪计数器变量的状态，并且只有当`beginContentAccess`方法返回`YES`时，才能访问内存。

The system or client objects call the [discardContentIfPossible](https://developer.apple.com/documentation/foundation/nsdiscardablecontent/1408998-discardcontentifpossible) method to discard the purgeable data if the system’s available memory is running low. This method will only discard the data if its counter variable is `0`, and otherwise does nothing. Lastly, the [isContentDiscarded](https://developer.apple.com/documentation/foundation/nsdiscardablecontent/1417470-iscontentdiscarded) method returns `YES` if the memory has been discarded.

Below is an example of a life cycle for an `NSPurgeableData` object:

```
NSPurgeableData * data = [[NSPurgeableData alloc] init];
[data endContentAccess]; //Don't necessarily need data right now, so mark as discardable.
//Maybe put data into a cache if you anticipate you will need it later.
 
...
 
if([data beginContentAccess]) { //YES if data has not been discarded and counter variable has been incremented
     ...Some operation on the data...
     [data endContentAccess] //done with the data, so mark as discardable
} else {
     //data has been discarded, so recreate data and complete operation
     data = ...
     [data endContentAccess]; //done with data
}
 
//data is able to be discarded at this point if memory is tight

```
### Purgeable Memory and NSCache
When objects that implement the `NSDiscardableContent` protocol are put in `NSCache` objects, the cache keeps a strong reference to the object. However, if an object’s content has been discarded and the cache’s [evictsObjectsWithDiscardedContent](https://developer.apple.com/documentation/foundation/nscache/1408469-evictsobjectswithdiscardedconten) value is set to `YES`, the object is automatically removed from the cache and is not found by a lookup call.
### Some Warnings About Purgeable Memory
A caveat to using purgeable memory is that only large objects or chunks of memory can use it directly. The purgeable memory API acts on multi page virtual memory objects, which makes it hard to mark a single small cache element as purgeable. The caching API will do the required bookkeeping to allow small cache elements to use purgeable memory. Likewise, there will be cases where it is awkward to allocate the memory for cache elements through the API directly, such as when a convenience method is used to allocate an object or when an object is allocated in a different layer than the layer doing the caching. In such cases, it is not possible to use purgeable memory.

## When to Use Purgeable Memory
It makes sense to use purgeable memory when the expected cost of purging is less than the expected cost of paging — when the cost of paging is greater than the cost of recomputing the particular data value times the probability that that data item is reused. Many caches fall into this category because their speculative nature makes it likely that the items will not be used again. Similarly, cache items that are easily recomputed are prime candidates for purgeable memory, because the application will not take a huge performance hit if their values have to be recalculated.


