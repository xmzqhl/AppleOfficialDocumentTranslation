## Build Settings

A build setting is a variable that contains information about how a particular aspect of a product’s build process should be performed. For example, the information in a build setting can specify which options Xcode passes to the compiler.

一个构建设置一个变量，包含了产品构建过程中一个特定的方面应该如何执行的信息。例如，构建设置中的信息可以执行Xcode将哪个选项传递给编译器。

You can specify build settings at the project or target level. Each project-level build setting applies to all targets in the project unless explicitly overridden by the build settings for a specific target.

你可以在工程或目标级别指定构建设置。每个工程级别的构建设置都适用于工程中的所有目标，除非被特定的目标的构建设置显式覆盖了。

Each target organizes the source files needed to build one product. A build configuration specifies a set of build settings used to build a target's product in a particular way. For example, it is common to have separate build configurations for debug and release builds of a product.

每个目标组织构建一个产品所需的源文件。一个构建配置指定一组用于以特定方式构建目标产品的构建设置。例如，通常有单独的构建配置用于产品的调试和发布构建。

A build setting in Xcode has two parts: the setting title and the definition. The build setting title identifies the build setting and can be used within other settings. The build setting definition is a constant or a formula Xcode uses to determine the value of the build setting at build time. A build setting may also have a display name, which is used to display the build setting in the Xcode user interface.

Xcode中的构建设置有两部分：设置标题和定义。构建的设置标题标识了构建设置，并可以用在其他设置里。构建设置定义是一个常量或xcode在构建时确定构建设置的值的公式。一个构建设置也可能有显示名称，用于在Xcode用户界面中显示构建设置。

In addition to the default build settings provided by Xcode when you create a new project from a project template, you can create user-defined build settings for your project or for a particular target. You can also specify conditional build settings. The value of a conditional build setting depends on whether one or more prerequisites are met. This mechanism allows you to, for example, specify the SDK to use to build a product based on the targeted architecture.

从工程模板创建新工程时，除了Xcode提供的默认构建设置外，您可以为您的工程或一个特定的目标创建用户定义的构建设置。你还可以指定条件构建设置。一个条件构建设置的值取决于是否满足一个或多个先决条件。例如，这个机制允许你可以基于目标的架构指定构建产品的SDK。

#### 先决条件的文章（Prerequisite Articles）

[Xcode Target](Xcode%20Target.md)

#### 相关文章（Related Articles）

[Xcode Project](Xcode%20Project.md)

[Xcode Scheme](Xocde%20Scheme.md)