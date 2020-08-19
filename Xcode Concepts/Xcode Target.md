## Xcode Target

A target specifies a product to build and contains the instructions for building the product from a set of files in a project or workspace. A target defines a single product; it organizes the inputs into the build system—the source files and instructions for processing those source files—required to build that product. Projects can contain one or more targets, each of which produces one product.

一个`target`指定要构建的产品(`product`)，并包含来自于`project`或`workspace`中的一组文件的关于构建该产品的说明。一个`target`定义单独的一个产品；它将输入组织到构建系统中，即构建该产品所需的源文件和处理这些文件的说明。`Projects`可以包含一个或多个`targets`,每个`target`产生一个产品。

The instructions for building a product take the form of build settings and build phases, which you can examine and edit in the Xcode project editor. A target inherits the project build settings, but you can override any of the project settings by specifying different settings at the target level. There can be only one active target at a time; the Xcode scheme specifies the active target.

构建一个产品的说明采用构建设置(`build setting`)和构建阶段(`build phase`)的形式，您可以在Xcode项目编辑器中检查和编辑。一个`target`继承`project`的构建设置，但是你可以在`target`级别指定不同的设置来覆盖任何在`project`中的设置。一次只能有一个激活的`target`，`Xcode scheme`指定了激活的`target`。

A target and the product it creates can be related to another target. If a target requires the output of another target in order to build, the first target is said to depend upon the second. If both targets are in the same workspace, Xcode can discover the dependency, in which case it builds the products in the required order. Such a relationship is referred to as an implicit dependency. You can also specify explicit target dependencies in your build settings, and you can specify that two targets that Xcode might expect to have an implicit dependency are actually not dependent. For example, you might build both a library and an application that links against that library in the same workspace. Xcode can discover this relationship and automatically build the library first. However, if you actually want to link against a version of the library other than the one built in the workspace, you can create an explicit dependency in your build settings, which overrides this implicit dependency.

一个`target`和它创建的产品可以被另一个`target`关联。如果一个`target`需要另一个`target`的输出才能构建，则称第一个`target`依赖于第二个`target`。如果这两个`target`都在同一个`workspace`里，Xcode可以发现这种依赖关系，它会按照需要顺序构建产品。这种关系成为隐式依赖。你还可以在你的构建设置中指定显式地依赖关系，并且可以指定xcode期望具有隐式依赖关系的两个`target`实际上是不依赖的。例如，您可能会在同一个`workspace`中构建一个库和一个链接到该库的应用。Xcode可以发现这种关系，并自动先构建该库。但是如果您实际上要链接到该库的一个特定版本，而不是`workspace`中构建的，您可以在您的构建设置中创建一个显式的依赖关系，这样子会覆盖此隐式依赖。

#### 相关文章(Related Articles)
[构建设置](Build%20Settings.md)

[Xcode Scheme](Xcode%20Scheme.md)

[Xcode Workspace](Xcode%20Workspace.md)