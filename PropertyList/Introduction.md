# Introduction to Property Lists
> **注意：** 本文档翻译于2018年01月27日。如果Apple在这之后更新了文档，可能出现翻译跟官方原文不匹配的情况。[原文](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/PropertyLists/Introduction/Introduction.html#//apple_ref/doc/uid/10000048-CJBGDEGD)

Property lists organize data into named values and lists of values using several object types. These types give you the means to produce data that is meaningfully structured, transportable, storable, and accessible, but still as efficient as possible. Property lists are frequently used by applications running on both OS X and iOS. The property-list programming interfaces for Cocoa and Core Foundation allow you to convert hierarchically structured combinations of these basic types of objects to and from standard XML. You can save the XML data to disk and later use it to reconstruct the original objects.

属性列表使用几种对象类型将数据组织到命名值和值列表中。这些类型为您提供了生成有意义的结构，可传输，可存储和可访问的数据的手段，但仍尽可能地高效。 属性列表经常被在OS X和iOS上运行的应用程序使用。 Cocoa和Core Foundation的属性列表编程接口允许您将这些基本对象类型的层次结构化组合转换为标准XML，或者从标准XML转换而来。您可以将XML数据保存到磁盘，之后使用它重建原始的对象。

This document describes property lists and their various representations, and how to work with them using both certain Foundation classes of Cocoa and Property List Services of Core Foundation.

本文档介绍了属性列表及其各种表示形式，以及如何使用Cocoa的某些Foundation类和Core Foundation的属性列表服务来处理它们。

> **Note:** The user defaults system, which you programmatically access through the [NSUserDefaults](https://developer.apple.com/documentation/foundation/nsuserdefaults) class, uses property lists to store objects representing user preferences. This limitation would seem to exclude many kinds of objects, such as [NSColor](https://developer.apple.com/documentation/appkit/nscolor) and [NSFont](https://developer.apple.com/documentation/appkit/nsfont) objects, from the user default system. But if objects conform to the [NSCoding](https://developer.apple.com/documentation/foundation/nscoding) protocol they can be archived to [NSData](https://developer.apple.com/documentation/foundation/nsdata) objects, which are property list–compatible objects. For information on how to do this, see “[Storing NSColor in User Defaults](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/DrawColor/Tasks/StoringNSColorInDefaults.html#//apple_ref/doc/uid/20001693)“; although this article focuses on `NSColor` objects, the procedure can be applied to any object that can be archived.

> **注意：** 通过`NSUserDefaults`类以编程方式访问的默认的用户系统使用属性列表来存储表示用户首选项的对象。这个限制似乎从用户默认系统中排除了许多种类的对象，比如`NSColor`和`NSFont`对象。 但是，如果对象符合`NSCoding`协议，则可以将其归档到属性列表兼容的`NSData`对象。有关如何执行此操作的信息，请参“[在用户默认值中存储NSColor](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/DrawColor/Tasks/StoringNSColorInDefaults.html#//apple_ref/doc/uid/20001693)”; 虽然本文重点介绍`NSColor`对象，但该过程可以应用于任何可以归档的对象。

## Organization of This Document 本文档的组织

This document consists of the following chapters:

本文档由以下章节组成：

* [Quick Start for Property Lists](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/PropertyLists/QuickStartPlist/QuickStartPlist.html#//apple_ref/doc/uid/10000048i-CH4-SW5) is a mini-tutorial on property lists, giving you a “hands-on” familiarity with XML property lists and the Objective-C serialization API.
	
	[属性列表快速入门](属性列表快速入门.md)是属性列表的迷你教程，可让您亲身体验XML属性列表和Objective-C序列化API。

* [About Property Lists](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/PropertyLists/AboutPropertyLists/AboutPropertyLists.html#//apple_ref/doc/uid/10000048i-CH3-SW2) explains what property lists are and when you should use them.

	[关于属性列表](关于属性列表.md)解释属性列表是什么以及什么时候使用它们。

* [Creating Property Lists Programmatically](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/PropertyLists/CreatePropListProgram/CreatePropListProgram.html#//apple_ref/doc/uid/10000048i-CH5-SW1) shows how you can create hierarchically structured property lists using the APIs of Cocoa and Core Foundation.

	[以编程方式创建属性列表](以编程方式创建属性列表.md)显示如何使用Cocoa和Core Foundation的API创建分层结构的属性列表
	
* [Understanding XML Property Lists](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/PropertyLists/UnderstandXMLPlist/UnderstandXMLPlist.html#//apple_ref/doc/uid/10000048i-CH6-SW1) describes the format of XML property lists.
	
	[了解XML属性列表](了解XML属性列表.md)描述了XML属性列表的格式。
* [Serializing a Property List]() discusses how to serialize and deserialize property lists between their runtime and static representations).

	[序列化一个属性列表](序列化一个属性列表.md)讨论了如何在运行时和静态表示之间序列化和反序列化属性列表）。

* [Reading and Writing Property-List Data](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/PropertyLists/ReadWritePlistData/ReadWritePlistData.html#//apple_ref/doc/uid/10000048i-CH8-SW1) describes how to save property lists to files or URL resources and how to restore them later.

	[读写属性列表数据](读写属性列表数据.md)描述如何将属性列表保存到文件或URL资源以及如何在以后还原它们。
* [Old-Style ASCII Property Lists](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/PropertyLists/OldStylePlists/OldStylePLists.html#//apple_ref/doc/uid/20001012-BBCBDBJE) is an appendix that describes the format of old-style (OpenStep) ASCII property lists.

	[旧式ASCII属性列表](旧式ASCII属性列表.md)是一个附录，它描述了旧式（OpenStep）ASCII属性列表的格式。