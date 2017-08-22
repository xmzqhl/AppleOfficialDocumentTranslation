# Introduction 介绍
[原文](https://developer.apple.com/library/content/documentation/Performance/Conceptual/ManagingMemory/ManagingMemory.html#//apple_ref/doc/uid/10000160-SW1)

> **注意:**本文档翻译于2017年8月22日，如果Apple在之后更新文档，可能出现翻译跟原文不匹配的情况。

Memory is an important system resource that all programs use. Programs must be loaded into memory before they can run and, while running, they allocate additional memory (both explicitly and implicitly) to store and manipulate program-level data. Making room in memory for a program’s code and data requires time and resources and therefore affect the overall performance of the system. Although you cannot avoid using memory altogether, there are ways to minimize the impact your memory usage has on the rest of the system.

内存是所有程序使用的重要资源。程序在运行之前必须被加载进内存，并且在运行中，它们开辟额外的内存(显式地或隐式地)来存储和操作程序级别的数据。在内存中为程序的代码和数据腾出空间需要时间和资源，因此影响系统的整体性能。尽管你不能完全避免使用内存，但是有一些方法可以最大限度地减少内存使用对系统其余部分的影响。

This document provides background information about the memory systems of OS X and iOS and how you use them efficiently. You can use this information to tune your program’s memory usage by ensuring you are allocating the right amount of memory at the right time. This document also provides tips on how to detect memory-related performance issues in your program.

本文档提供有关OS X和iOS的内存系统的背景信息，以及如何有效的使用它们。你可以使用此信息通过在正确的时间开辟适量的内存，来调整程序的内存使用。本文档还提供了有关如何在程序中检测与内存相关的性能问题的小贴士。

## Organization of This Document 本文档的结构
This programming topic includes the following articles:

* [About the Virtual Memory System](https://developer.apple.com/library/content/documentation/Performance/Conceptual/ManagingMemory/Articles/AboutMemory.html#//apple_ref/doc/uid/20001880-BCICIHAB) introduces the terminology and provides a high-level overview of the virtual memory systems of OS X and iOS.

     [关于虚拟内存系统](AboutTheVirtualMemorySystem.md)介绍了OS X和iOS的虚拟内存系统的术语和高级概述。

* [Tips for Allocating Memory](https://developer.apple.com/library/content/documentation/Performance/Conceptual/ManagingMemory/Articles/MemoryAlloc.html#//apple_ref/doc/uid/20001881-CJBCFDGA) describes the best techniques for allocating, initializing, and copying memory. It also describes the proper ways to respond to low-memory notifications in iOS.

	[分配内存的技巧](TipsForAllocatingMemory.md)描述了分配、初始化和复制内存的最佳技术。它还描述了在iOS中响应低内存通知的正确方法。
      
* [Caching and Purgeable Memory](https://developer.apple.com/library/content/documentation/Performance/Conceptual/ManagingMemory/Articles/CachingandPurgeableMemory.html#//apple_ref/doc/uid/TP40013104-SW1) discusses the benefits of caching, and how to avoid some of the problems that can arise from implementing caches. It also details the advantages of implementing purgeable memory into a caching system and how to successfully implement this beneficial technology.

    [缓存和可清除内存](CachingAndPurgeableMemory.md)讨论缓存的好处，以及如何避免实现缓存可能产生的一些问题。它还描述了实现可擦除内存到一个缓存系统中的优势，和如何成功实现这一有益的技术。

* [Tracking Memory Usage](https://developer.apple.com/library/content/documentation/Performance/Conceptual/ManagingMemory/Articles/FindingPatterns.html#//apple_ref/doc/uid/20001882-CJBJFIDD) describes the tools and techniques for analyzing your application’s memory usage.

	[跟踪内存使用](TrackingMemoryUsage.md)描述了分析你应用程序内存使用情况的工具和技术

* [Finding Memory Leaks](https://developer.apple.com/library/content/documentation/Performance/Conceptual/ManagingMemory/Articles/FindingLeaks.html#//apple_ref/doc/uid/20001883-CJBJFIDD) describes the tools and techniques for finding memory leaks in your application.


	[查找内存泄露](FindingMemoryLeaks.md)描述了在你应用程序中找出内存泄露的工具和技术。

* [Enabling the Malloc Debugging Features](https://developer.apple.com/library/content/documentation/Performance/Conceptual/ManagingMemory/Articles/MallocDebug.html#//apple_ref/doc/uid/20001884-CJBJFIDD) describes the environment variables used to enable malloc history logging. You must set some of these variables before using some of the memory analysis tools.

     [开启Malloc调试功能](EnablingTheMallocDebuggingFeatures.md)描述了启用malloc历史记录的环境变量。在使用某些内存分析工具之前，你必须先设置其中的一些变量。

* [Viewing Virtual Memory Usage](https://developer.apple.com/library/content/documentation/Performance/Conceptual/ManagingMemory/Articles/VMPages.html#//apple_ref/doc/uid/20001985-CJBJFIDD) describes the tools and techniques for analyzing your application’s in-memory footprint.

	[查看虚拟内存使用](ViewingVirtualMemoryUsage.md)描述了分析您应用程序内存中足迹的工具和技术。