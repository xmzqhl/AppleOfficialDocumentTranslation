## Xcode Project
An Xcode project is a repository for all the files, resources, and information required to build one or more software products. A project contains all the elements used to build your products and maintains the relationships between those elements. It contains one or more targets, which specify how to build products. A project defines default build settings for all the targets in the project (each target can also specify its own build settings, which override the project build settings).

一个Xcode工程是构建一个或多个软件产品所需的所有文件、资源、构建信息的存储库。一个工程包含所有用来构建您的产品的元素以及维护那些元素之间的关系。它包含一个或多个指定如何构建产品的`target`。一个工程为在工程内的所有的`target`定义了默认的构建设置(每个`target`也可以指定自己的构建设置来覆盖工程的构建设置)。

An Xcode project file contains the following information:

一个xcode工程文件包含以下信息：

* 对源文件的引用(References to source files:)

	* Source code, including header files and implementation files（源代码，包括头文件和实现文件）
	
	* Libraries and frameworks, internal and external（内部和外部的库和框架）

	* Resource files（资源文件）
	
	* Image files（图片文件）
	
	* Interface Builder (nib) files（nib文件）
	
* Groups used to organize the source files in the structure navigator
* 用于在结构导航器中组织源文件的组
* Project-level build configurations. You can specify more than one build configuration for a project; for example, you might have debug and release build settings for a project.
* 工程级别的构建配置。你可以为一个工程指定多个构建配置。例如，您可能为一个工程有调试和发布的构建设置。
* Targets, where each target specifies:

	* A reference to one product built by the project
	
	* References to the source files needed to build that product
	
	* The build configurations that can be used to build that product, including dependencies on other targets and other settings; the project-level build settings are used when the targets’ build configurations do not override them
	
* 目标，每个目标都指定了：

	* 工程构建的一个产品的引用
	
	* 对构建该产品所需的源文件的引用
	
	* 可用于构建该产品的构建配置，包括对其他设置和其他目标的依赖；工程级别的构建设置将用于当目标的构建配置没有覆盖他们。

* The executable environments that can be used to debug or test the program, where each executable environment specifies:

	* What executable to launch when you run or debug from Xcode

	* Command-line arguments to be passed to the executable, if any

	* Environmental variables to be set when the program runs, if any
	
* 用户调试和测试程序的可执行环境，每个可执行环境指定了：

	* 从Xcode运行或调试时要启动的可执行文件
	* 传递给可执行文件的命令行参数，如果有的话
	* 当程序运行时要被设置的环境变量，如果有的话

A project can stand alone or can be included in a workspace.

You use Xcode schemes to specify which target, build configuration, and executable configuration is active at a given time.

一个工程可以独立存在，或者包含在workspace里面。

在一个给定的时间，你使用`Xcode scheme`来指定哪个目标、构建配置和可执行配置是激活的。

#### 相关文章（Related Articles）

[Xcode Workspace](Xcode%20Workspace.md)

[Target](Xcode%20Target.md)

[Build Setting](Build%20Settings.md)

[Xcode Scheme](Xcode%20Scheme.md)