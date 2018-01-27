# Old-Style ASCII Property Lists 旧式ASCII属性列表

The OpenStep frameworks, which Cocoa is derived from, used an ASCII format for storing property lists. These files store information equivalent to the information in XML property lists, and are still supported by Cocoa, but for reading only. Old-style plist support remains primarily for legacy reasons. You read old-style property lists by calling the `propertyListFromData:mutabilityOption:format:errorDescription:` method of `NSPropertyListSerialization`.

Cocoa派生自的OpenStep框架使用ASCII格式来存储属性列表。 这些文件存储的信息等同于XML属性列表中的信息，并且仍然受Cocoa支持，但仅供读取。 旧式的plist支持主要是为了传统的原因。 您通过调用`NSPropertyListSerialization`的`propertyListFromData:mutabilityOption:format:errorDescription:`方法来读取旧式属性列表。

ASCII property lists support the four primary property list data types: NSString, NSData, NSArray, and NSDictionary. The following sections describe the ASCII syntax for each of these types.

ASCII属性列表支持四个主要属性列表数据类型：NSString，NSData，NSArray和NSDictionary。 以下各节介绍每种类型的ASCII语法。

> **Important:** Cocoa allows you to read old-style ASCII property lists only. You may not write them.

>**重要提示：** Cocoa只允许您读取旧式ASCII属性列表。你不可能会写入它们。

## NSString

A string is enclosed in double quotation marks, for example:

一个字符串用双引号括起来，例如：

```
"This is a string"
```

The quotation marks can be omitted if the string is composed strictly of alphanumeric characters and contains no white space (numbers are handled as strings in property lists). Though the property list format uses ASCII for strings, note that Cocoa uses Unicode. Since string encodings vary from region to region, this representation makes the format fragile. You may see strings containing unreadable sequences of ASCII characters; these are used to represent Unicode characters.

如果字符串严格由字母数字字符组成并且不包含空格（数字在属性列表中作为字符串处理），则可以省略引号。尽管属性列表格式为字符串使用ASCII，但请注意，Cocoa使用Unicode。由于字符串编码因区域而异，因此这种表示使得格式变得脆弱。 您可能会看到包含不可读的ASCII字符序列的字符串; 这些通常是用Unicode字符来表示的。

## NSData

Binary data is enclosed in angle brackets and encoded in hexadecimal ASCII. Spaces are ignored. For example:

二进制数据用尖括号括起来，并用十六进制ASCII编码。空格被忽略。 例如：

```
<0fbd777 1c2735ae>
```

## NSArray

An array is enclosed in parentheses, with the elements separated by commas. For example:

数组括在圆括号中，元素用逗号分隔。 例如：

```
("San Francisco", "New York", "Seoul", "London", "Seattle", "Shanghai")
```

The items don’t all have to be of the same type (for example, all strings)—but they normally are. Arrays can contain strings, binary data, other arrays, or dictionaries.

这些项目不一定都是相同的类型（例如，所有都是字符串） - 但他们通常是。 数组可以包含字符串，二进制数据，其他数组或字典。

## NSDictionary

A dictionary is enclosed in curly braces, and contains a list of keys with their values. Each key-value pair ends with a semicolon. For example:

一个字典用大括号括起来，并包含一个键值列表。 每个键值对以分号结尾。 例如：

```
{ user = wshakesp; birth = 1564; death = 1616; }
```

Note the omission of quotation marks for single-word alphanumeric strings. Values don’t all have to be the same type, because their types are usually defined by whatever program uses them. Dictionaries can contain strings, binary data, arrays, and other dictionaries.

请注意省略了单字母 数字字母字符串的引号。 值不必都是相同的类型，因为它们的类型通常由任何使用它们的程序来定义。字典可以包含字符串，二进制数据，数组和其他字典。

Below is a sample of a more complex property list. The property list itself is a dictionary with keys “AnimalSmells,” “AnimalSounds,” and so on; each value is also a dictionary, with key-value pairs.

下面是一个更复杂的属性列表的示例。 属性列表本身是一个字典，其中包括“AnimalSmells”，“AnimalSounds”等等。 每个值也是一个字典，带有键值对。

```
{
    AnimalSmells = { pig = piggish; lamb = lambish; worm = wormy; };
    AnimalSounds = { pig = oink; lamb = baa; worm = baa;
                    Lisa = "Why is the worm talking like a lamb?"; };
    AnimalColors = { pig = pink; lamb = black; worm = pink; };
}
```

