# Introduction 介绍

> **注意：**本文档翻译于2018年01月24日。如果Apple在这之后更新了文档，可能出现翻译跟官方原文不匹配的情况。[原文](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Archiving/Archiving.html#//apple_ref/doc/uid/10000047-SW1)

Archives and serializations are two ways in which you can create architecture-independent byte streams of hierarchical data. Byte streams can then be written to a file or transmitted to another process, perhaps over a network. When the byte stream is decoded, the hierarchy is regenerated. Archives provide a detailed record of a collection of interrelated objects and values. Serializations record only the simple hierarchy of property-list values.

归档和序列化是您创建体系结构独立的分层数据字节流的两种方式。字节流之后可以写入文件或者传输到另一个进程，或许通过网络。当字节流被解码时，这个层次被重新生成。归档提供了一系列相关对象和值的详细记录。序列化仅记录属性列表值的简单层次。

You should read this document to learn how to create and extract archived representations of object graphs.

你应该阅读本文档以了解如何创建和提取对象图的归档表示。

## Organization of This Document 本文档的组织

This programming topic contains the following articles:

本编程主题包含以下文章：

* [Object Graphs](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Archiving/Articles/objectgraphs.html#//apple_ref/doc/uid/20001293-CJBDFIBI) introduces the concept of an object graph and discusses the two techniques for turning objects into byte streams: archives and serializations. 
                                                        
 [对象图](对象图.md) 引入了对象图的概念，并讨论了将对象转变为字节流的两种技术：归档和序列化。

* [Archives](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Archiving/Articles/archives.html#//apple_ref/doc/uid/20000946-BAJDBJAI) describes the different types of archive and archiver classes

 [归档](归档.md) 介绍了不同类型的归档和归档类。

* [Creating and Extracting Archives](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Archiving/Articles/creating.html#//apple_ref/doc/uid/20000949-BABGBHCA) describes how to create and extract an archive.
 
 [创建和提取归档](创建和提取归档.md) 介绍如何创建和提取一个归档。
 
* [Encoding and Decoding Objects](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Archiving/Articles/codingobjects.html#//apple_ref/doc/uid/20000948-BCIHBJDE) describes how to implement the methods that allow an object to be encoded in and decoded from archives.

 [编码和解码对象](编码和解码对象.md) 介绍了如何实现允许一个对象被编码到归档和从归档中解码的方法。
 
* [Encoding and Decoding C Data Types](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Archiving/Articles/codingctypes.html#//apple_ref/doc/uid/20001294-BBCBDHBI) describes how to encode and decode C data types that do not have convenience methods defined in the archive classes.

 [编码和解码C数据类型](编码和解码C数据类型.md) 介绍了如何编码和解码 在归档类中没有定义便利方法的C数据类型。
 
* [Forward and Backward Compatibility for Keyed Archives](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Archiving/Articles/compatibility.html#//apple_ref/doc/uid/20001055-BCICFFGE) provides some tips on how to make your classes more compatible with previous and future versions of your classes in keyed archives.

	[键控归档的向前和向后兼容](键控归档的向前和向后兼容.md) 提供了一些技巧，让你的类在键控归档中与以前的和未来版本更加兼容。
	
* [Subclassing NSCoder](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Archiving/Articles/subclassing.html#//apple_ref/doc/uid/20000951-BABEIEHG) provides some tips on how to create your own coder classes.

 [子类化NSCoder](子类化NSCoder.md) 提供了一些关于如何创建自己的编码器类的提示。

* [Serializing Property Lists](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Archiving/Articles/serializing.html#//apple_ref/doc/uid/20000952-BABBEJEE) describes how to create and read serialized representations of a property list
 
 [序列化属性列表](序列化属性列表.md) 介绍了如何创建和读取一个属性列表的的序列化表示。