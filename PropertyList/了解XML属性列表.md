# Understanding XML Property Lists 了解XML属性列表

The preferred way to store property lists on OS X and iOS is as an XML file called an XML property list or XML plist. These files have the advantages of being human-readable and in the standards-based XML format. The [NSArray](https://developer.apple.com/documentation/foundation/nsarray) and [NSDictionary](https://developer.apple.com/documentation/foundation/nsdictionary) classes both have methods for saving themselves as XML plists (for example, [descriptionWithLocale:](https://developer.apple.com/documentation/foundation/nsarray/1412374-description) and [writeToFile:atomically:](https://developer.apple.com/documentation/foundation/nsdictionary/1415753-write)), and also have methods to convert XML property lists back to objects in memory. CFPropertyList provides functions for converting property lists to and from their XML representation.

在OS X和iOS上存储属性列表的首选方式是称为XML属性列表或XML plist的XML文件。这些文件的优点是可读性强，采用基于标准的XML格式。 [NSArray](https://developer.apple.com/documentation/foundation/nsarray) 和 [NSDictionary](https://developer.apple.com/documentation/foundation/nsdictionary)类都有将自己保存为XML plists的方法（例如，[descriptionWithLocale:](https://developer.apple.com/documentation/foundation/nsarray/1412374-description) 和 [writeToFile:atomically:](https://developer.apple.com/documentation/foundation/nsdictionary/1415753-write))，并且还有方法将XML属性列表转换回内存中的对象。 CFPropertyList提供了将属性列表转换为和从它们的XML表示中转换的函数。

Core Foundation supports XML as the exclusive medium for the static representation of property lists on disk. Cocoa allows property lists to be stored on disk as XML property lists, in binary form, and as “old-style” property lists. The old-style property lists can only be read, not written; see [Old-Style ASCII Property Lists](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/PropertyLists/OldStylePlists/OldStylePLists.html#//apple_ref/doc/uid/20001012-BBCBDBJE) for further information.

Core Foundation支持将XML作为磁盘上属性列表的静态表示的唯一媒介。 Cocoa允许属性列表以XML属性列表,二进制形式, 和作为“旧式”属性列表存储在磁盘上。 旧式的属性列表只能被读取，不能写入; 有关更多信息，请参阅[旧式ASCII属性列表](旧式ASCII属性列表.md)。

Generally, there is little need to create or edit XML property yourself, but if you do, use Xcode’s built-in property list editor or the Property List Editor application (which is part of the tools package). You should not edit the XML data in a text editor unless you are very familiar with XML syntax and the format of property lists. Moreover, the elements in an XML property list could change in future releases, so keep that in mind.

一般来说，几乎不需要自己创建或编辑XML属性，但是如果需要，可以使用Xcode的内置属性列表编辑器或Property List Editor应用程序（它是工具包的一部分）。除非您非常熟悉XML语法和属性列表的格式，否则不应该在文本编辑器中编辑XML数据。此外，XML属性列表中的元素可能会在将来的版本中发生变化，请记住这一点。

Even if you don’t edit XML property lists, it is useful to understand their structure for design and debugging purposes. Like every XML file, XML plists begin with standard header information, and contain one root object, wrapped with the `<plist>` document type tag. The `<plist>` object also contains exactly one object, denoted by one of the XML elements listed in [Table 2-1](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/PropertyLists/AboutPropertyLists/AboutPropertyLists.html#//apple_ref/doc/uid/10000048i-CH3-SW1).

即使您不编辑XML属性列表，了解其结构对设计和调试目的来说也是非常有用的。像每个XML文件一样，XML plists从标准头信息开始，包含一个根对象，用`<plist>`文档类型标签包装。 `<plist>`对象也只包含一个对象，由[表2-1](关于属性列表.md#table2)中列出的一个XML元素表示。

Graphs of objects are created by nesting the XML elements listed in Table 2-1. When encoding dictionaries, the element `<key>` is used for dictionary keys, and one of the other property list tags is used for the key’s value. Here is an example of XML data generated from a property list:

对象图是通过嵌套表2-1中列出的XML元素创建的。编码字典时，元素`<key>`用于字典键，而其他属性列表标签之一用于键的值。下面是从属性列表生成的XML数据的示例：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist SYSTEM "file://localhost/System/Library/DTDs/PropertyList.dtd">
<plist version="1.0">
<dict>
    <key>Author</key>
    <string>William Shakespeare</string>
    <key>Lines</key>
    <array>
        <string>It is a tale told by an idiot,</string>
        <string>Full of sound and fury, signifying nothing.</string>
    </array>
    <key>Birthdate</key>
    <integer>1564</integer>
</dict>
</plist>

```

Note that data bytes are base-64 encoded between the `<data>` and `</data>` tags.

请注意，data字节在`<data>`和`</data>`标签之间以base-64编码。
