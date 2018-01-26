# Subclassing NSCoder 子类化NSCoder
`NSCoder`’s interface is quite general and extensive, declaring methods to encode and decode objects and values with and without keys. Concrete subclasses are not required to properly implement all of `NSCoder`’s methods and may explicitly restrict themselves to certain types of operations. For example, `NSArchiver` does not implement the `decode...` methods, and `NSUnarchiver` does not implement the `encode...` methods. In addition, neither class implements the keyed coding methods for encoding and decoding keyed archives. Invoking a `decode` method on `NSArchiver` or an `encode` method on `NSUnarchiver` raises an `NSInvalidArgumentException`.

`NSCoder`的接口是相当通用和广泛的，声明了方法来通过有和没有键编码和解码对象和值。具体子类不需要正确实现所有的`NSCoder`方法，并可以明确地限制自己某些类型的操作。例如，`NSArchiver`没有实现`decode...`方法，`NSUnarchiver`没有实现`encode...`方法。另外，这两个类都不实现编码和解码键控归档的键控编码方法。调用`NSArchiver`上的`decode`方法或`NSUnarchiver`上的`encode`方法引发一个`NSInvalidArgumentException`。

If you define a subclass of NSCoder that does not support keyed coding, at a minimum your subclass must override the following methods:

如果你定义了一个不支持键控编码的`NSCoder`的子类，你的子类必须至少覆盖下面的方法：

`encodeValueOfObjCType:at:`

`decodeValueOfObjCType:at:`

`encodeDataObject:`

`decodeDataObject`

`versionForClassName:`


If your subclass supports keyed coding, you must override the above methods as well as the `allowsKeyedCoding` method (to return `YES`) and all of the keyed coding methods defined by `NSCoder`. In both cases, if you are creating separate classes for encoding and decoding, you do not need to override the encode methods in the decoder class nor the decode methods in the encoder class.

如果你的子类支持键控编码，你必须覆盖上面的方法以及`allowsKeyedCoding`方法（返回`YES`）和所有`NSCoder`定义的键控编码方法。在这两种情况下，如果要为编码和解码创建单独的类，则无需覆盖解码器类中的编码方法，也不需要覆盖编码器类中的解码方法。

Note that `encodeObject:` and `ecodeObject` are not among the basic methods. They are defined abstractly to invoke `encodeValueOfObjCType:at:` or `decodeValueOfObjCType:at:` with an Objective-C type code of “@”. Your implementations of the latter two methods must handle this case, invoking the object’s `encodeWithCoder:` or `initWithCoder:` method and sending the proper substitution messages (as described in [Making Substitutions During Coding](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Archiving/Articles/codingobjects.html#//apple_ref/doc/uid/20000948-97072)) to the object before encoding it and after decoding it.

请注意`encodeObject:` 和 `ecodeObject`不在基本的方法之中。它们被抽象地定义为使用Objective-C类型编码“@”来调用`encodeValueOfObjCType:at:` 或 `decodeValueOfObjCType:at:`。 你自己后两种方法的实现必须处理这种情况，调用对象的`encodeWithCoder:` 或 `initWithCoder:`方法，并在对对象进行编码之前和解码之后发送适当的替换消息（如在[编码期间进行替换](编码和解码对象.md#MakingSubstitutionsDuringCoding)中所述）。

Your subclass may override other methods to provide specialized handling for certain situations. In particular, you can implement any of the following methods:

您的子类可能会覆盖其他方法来为某些情况提供专门的处理。 特别是，您可以实现以下的任意方法：

`encodeRootObject:`

`encodeConditionalObject:`

`encodeBycopyObject:`

`encodeByrefObject:`

See the individual method descriptions for more information on their required behavior. The default `NSCoder` implementations of these methods just invoke `encodeObject:`.

有关其所需行为的更多信息，请参阅各个方法说明。`NSCoder`这些方法的默认实现只是调用`encodeObject:`.

If you override `encodeConditionalObject:` to support conditional objects (see [Conditional Objects](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Archiving/Articles/archives.html#//apple_ref/doc/uid/20000946-142208)), be aware that the first unconditional encoding may occur after any number of conditional encoding requests, so your coder will not know which conditional objects to encode until all the other objects have been encoded.

如果您重载`encodeConditionalObject:`以支持条件对象（请参阅[条件对象](归档.md#ConditionalObjects)），请注意，第一个非条件编码可能发生在任意数量的条件编码请求之后，因此编码器将不知道要编码哪些条件对象，直到所有其他对象被编码。

With objects, the object being coded is fully responsible for coding itself. However, a few classes hand this responsibility back to the coder object, either for performance reasons or because proper support depends on more information than the object itself has. The notable classes in Foundation that do this are `NSData` and `NSPort`. `NSData`’s low-level nature makes optimization important. For this reason, an `NSData` object always asks its coder to handle its contents directly using the `encodeDataObject:` and `decodeDataObject` methods when it receives the `encodeWithCoder:` and `initWithCoder:` messages. Similarly, an `NSPort` object asks its coder to handle it using the `encodePortObject:` and `decodePortObject` methods (which only `NSPortCoder` implements). This is because an `NSPort` represents information kept in the operating system itself, which requires special handling for transmission to another process.

通过对象，被编码的对象完全负责编码它自己。然而，有几个类将这个责任交给编码器对象，无论是出于性能原因还是因为适当的支持依赖于比对象本身拥有的更多的信息。 Foundation中值得注意的类是`NSData`和`NSPort`。 `NSData`的低级特性使得优化非常重要。由于这个原因，当`NSData`对象接收到`encodeWithCoder:`和`initWithCoder:`消息时，总是要求其编码器直接使用`encodeDataObject:`和`decodeDataObject`方法处理其内容。 同样，一个`NSPort`对象要求其编码器使用`encodePortObject:`和`decodePortObject`方法（只有`NSPortCoder`实现了）来处理它。这是因为`NSPort`代表的信息是被操作系统本身持有的，需要特殊处理才能传输到另一个进程。

These special cases don’t affect users of coder objects, since the redirection is handled by the classes themselves in their `NSCoding` protocol methods. An implementor of a concrete coder subclass, however, must implement the appropriate custom methods to encode and decode `NSData` and (if relevant) `NSPort` objects itself.

这些特殊情况不会影响编码器对象的用户，因为重定向是由类本身在它们的`NSCoding`协议方法中处理的。然而，具体编码器子类的实现者必须实现适当的自定义方法来编码和解码`NSData`以及（如果相关）`NSPort`对象本身。