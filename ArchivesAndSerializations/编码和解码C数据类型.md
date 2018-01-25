# Encoding and Decoding C Data Types 编码和解码C数据类型
`NSKeyedArchiver` and `NSKeyedUnarchiver` provide a number of methods for handling non-object data. Integers can be encoded with `encodeInt:forKey:`, `encodeInt32:forKey:`, or `encodeInt64:forKey:`. Likewise, real values can be encoded with `encodeFloat:forKey:` or `encodeDouble:forKey:`. Other methods encode booleans and byte arrays. The classes also provide several convenience methods for handling special data types used in Cocoa, such as `NSPoint`, `NSSize`, and `NSRect`.

`NSKeyedArchiver`和`NSKeyedUnarchiver`提供了许多用于处理非对象数据的方法。 整数可以使用`encodeInt:forKey:`, `encodeInt32:forKey:`或`encodeInt64:forKey:`进行编码。 同样，实数可以用`encodeFloat:forKey:`或者`encodeDouble:forKey:`进行编码。 其他方法编码布尔值和字节数组。这些类还提供了几种处理Cocoa中使用的特殊数据类型的便利方法，例如`NSPoint`，`NSSize`和`NSRect`。

`NSKeyedArchiver` and `NSKeyedUnarchiver` do not provide methods for encoding and decoding aggregate types, such as structures, arrays, and bit fields. The following sections provide suggestions on how to handle unsupported data types.

`NSKeyedArchiver`和`NSKeyedUnarchiver`不提供编码和解码聚合类型的方法，如结构体，数组和位域。以下部分提供了有关如何处理不受支持的数据类型的建议。

## Pointers 指针
You can’t encode a pointer and get back something useful at decode time. You have to encode the information to which the pointer is pointing. This is true in non-keyed coding as well.

你不能编码一个指针，并在解码时找回有用的价值。您必须编码指针指向的信息。在非键控编码中也是如此。

Pointers to C strings (`char *`) are a special case because they can be treated as a byte array which can be encoded using `encodeBytes:length:forKey:`. You can also wrap C strings with a temporary `NSString` object and archive the string. Reverse the process when decoding. Be sure to keep in mind the character set encoding of the string when creating the `NSString` object, and chose an appropriate creation method.

指向C字符串（`char *`）的指针是一个特殊情况，因为它们可以被当作一个可以使用`encodeBytes:length:forKey:`进行编码的字节数组. 你也可以用一个临时的`NSString`对象包装C字符串并存档字符串。解码时反转这个过程。在创建`NSString`对象时一定要记住字符串的字符集编码，并选择适当的创建方法。

## Arrays of Simple Types 简单类型的数组
If you are encoding an array of bytes, you can just use the provided methods to do so.

如果你正在编码一个字节数组，你可以使用提供的方法来完成。

For other arithmetic types, create an `NSData` object with the array. *Note that in this case, dealing with platform endianness issues is your responsibility*. Platform endianness can be handled in two general ways. The first technique is to convert the elements of the array (or rather, a temporary copy of the array) into a canonical endianness, either big or little, one at a time with the functions discussed in Swapping Bytes in *Universal Binary Programming Guidelines, Second Edition* (see also “Byte Ordering” in *Foundation Functions Reference*) and give that result to the `NSData` as the buffer. (Or, you can write the bytes directly with `encodeBytes:length:forKey:`.) At decode time, you have to reverse the process, converting from the big or little endian canonical form to the current host representation. The other technique is to use the array as-is and record in a separate keyed value (perhaps a boolean) which endianness the host was when the archive was created. During decoding, read the endian key and compare it to the endianness of the current host and swap the values only if different.

对于其他算术类型，使用该数组创建一个`NSData`对象。*请注意，在这种情况下，处理平台字节顺序问题是您的责任*。平台的字节顺序可以用两种常用的方法来处理。第一种技术是将数组的元素（或者说，数组的临时副本）转换为规范的字节顺序，不管是大端还是小端，每次一个地使用在*通用二进制编程指南*中的交换字节，第二版（另请参阅*Foundation函数引用*中的“字节顺序”）中讨论的函数，并将该结果作为缓冲区提供给`NSData`。 （或者，您可以直接使用encodeBytes：length：forKey：来编写字节。）在解码时，您必须反转该过程，将大小规范形式转换为当前主机表示形式。另一种方法是直接使用数组，并在一个单独的键值（可能是布尔值）中记录主机在创建存档时的字节顺序。在解码过程中，读取字节顺序的键并将其与当前主机的字节顺序进行比较，只有在不同的情况下才交换值。

Alternatively, you can archive each array element separately as their native type, perhaps by using key names inspired by the array syntax, like “theArray[0]”, “theArray[1]”, and so on. This is not a terribly efficient technique, but you can ignore endian issues.

或者，可以将每个数组元素单独存档为其本机类型，也许可以使用受数组语法启发的键名，如“theArray[0]”，“theArray[1]”等。这不是一个非常有效的技术，但你可以忽略字节顺序问题。

## Arrays of Objects 对象数组
The simplest thing to do for a C array of objects is to temporarily wrap the array in an `NSArray` object with `initWithObjects:count:`, encode the array object, then get rid of the object. Because objects contain other information that has to be encoded, you can’t just embed the array of pointers in an `NSData` object; each object must be individually archived. During decoding, use `getObjects:` on the retrieved array to get the objects back out into an allocated C array (of the correct size).

对于C对象数组最简单的做法是用`initWithObjects:count:`来临时将数组包装在一个`NSArray`对象中，对数组对象进行编码，然后删除这个对象。由于对象包含必须进行编码的其他信息，因此不能将指针数组嵌入到`NSData`对象中; 每个对象都必须单独存档。在解码期间，在检索的数组上使用`getObjects:`将对象返回到分配的C数组（大小正确）。

## <a name="StructuresandBitFields"></a>Structures and Bit Fields 结构体和位域
The best technique for archiving a structure or a collection of bit fields is to archive the fields independently and choose the appropriate type of encoding/decoding method for each. The key names can be composed from the structure field names if you wish, like “theStruct.order”, “theStruct.flags”, and so on. This creates a slight dependency on the names of the fields in the source code, which over time may get renamed, but the archiving keys cannot change if you want to maintain compatibility.

归档一个结构体或者一个位域的集合的最佳手段是独立的归档该字段,并为每个选择合适的编码/解码方法类型。如果您愿意，键名可以由结构字段名构成，如“theStruct.order”，“theStruct.flags”等。 这会对源代码中的字段名称产生轻微的依赖关系，字段名随着时间的推移可能会重新命名，但是如果要保持兼容性，则归档的键不能更改。

You should not wrap a structure with an `NSData` object and archive that. If the structure contains object or pointer fields, the data object isn’t going to archive them correctly. You also create a dependence on how the compiler decides to lay out the structure, which can change between versions of the compiler and may depend on other factors. A compiler is not constrained to organize a structure just as you’ve specified it in the source code—there may be arbitrary internal and invisible padding bytes between fields in the structure, for example, and the amount of these can change without notice and on different platforms. In addition, any fields that are multiple bytes in width aren’t going to get treated correctly with respect to endianness issues. You will cause yourself all sorts of compatibility trouble.

你不应该用一个`NSData`对象包装一个结构体，并将其存档。如果结构包含对象或指针字段，则数据对象不会正确存档。您还创建了编译器如何确定布局结构的依赖关系，编译器的不同版本可能会发生变化，并可能取决于其他因素。编译器并没有像在源代码中指定的那样组织一个结构-例如，结构中的字段之间可能有任意的内部和不可见的填充字节，并且这些字节的数量可以在没有通知和不同的平台上改变。另外，任何宽度为多个字节的字段在字节顺序问题上都不会得到正确的处理。你会导致你自己的各种兼容性问题。

Likewise, bit fields should never be encoded by reading the raw bits of several bit fields as an integer and encoding the integer. (Encoding an integer that you construct manually from several bit fields, using bit shifts and OR operations, however, avoids most of the pitfalls that follow.) Although there are some requirements on compilers specified in the C standard, a compiler still has some freedom in how things are actually organized and which bits it chooses to store where, and what bits it may choose not to use (inter-field padding bits). The location of those bits could differ between compilers or change as a particular compiler evolves. On top of this, you also have to deal with endianness issues. The order of the bits within an integer could be different for the machine that encodes the archive and the machine that decodes it. Finally, by encoding the raw bits, you constrain future development of your class to use the same bit field sizes as the oldest archive you need to support. Otherwise, you have to be able to parse the old bit stream and initialize the new bit stream yourself, handling the compiler and platform issues appropriately.

同样，不应该通过读取几个位域的原始位作为整数并对整数进行编码来编码位域。（然而，使用位移和OR操作对从几个位字段手动构建的整数进行编码，可避免以下大部分的缺陷）。尽管对C标准中指定的编译器有一些要求，编译器仍然有一些自由度，在实际上事情是如何组织的，它选择存储哪些位以及它可能选择不使用哪些位（字段间填充位）。这些位的位置在编译器之间可能有所不同，或者随着特定的编译器的发展而改变。最重要的是，你还必须处理字节顺序问题。对整数进行编码的机器和对其解码的机器，整数内的位顺序可能不同。最后，通过对原始比特进行编码，你限制类的未来发展，以便使用与您需要支持的最旧的归档相同的比特字段大小。否则，您必须能够解析旧的比特流，并自己初始化新的比特流，以适当地处理编译器和平台问题。

As is the case for an object’s instance variables in general, it is not necessary to archive every field of a structure or bit field. You only need to encode and decode the fields required to preserve a structure’s state. Fields that are calculated or otherwise derived by other means should not be archived.

一般情况下，一个对象的实例变量就是这样，不需要对结构体或比特字段的每个字段进行归档。您只需编码和解码保留结构体状态所需的字段。 通过其他方式计算或以其他方式推导出的字段不应归档。

## More Complex Data Types 更复杂的数据类型
More complex data types, such as arrays of aggregates, can generally be handled using the techniques for simple data types and combining them with custom logic for your particular application.

更复杂的数据类型（如聚合数组）通常可以使用简单数据类型的技术进行处理，并将它们与特定应用程序的自定义逻辑相结合。