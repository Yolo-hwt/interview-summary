## webpack做了什么？

- **模块打包。**

可以将不同模块的文件打包整合在一起，并且保证它们之间的**引用正确，执行有序**。

利用打包我们就可以在开发的时候根据我们自己的业务**自由划分文件模块，保证项目结构的清晰和可读性。**

- **编译兼容**

在前端的“上古时期”，手写一堆浏览器兼容代码一直是令前端工程师头皮发麻的事情，而在今天这个问题被大大的弱化了

通过`webpack`的`Loader`机制，不仅仅可以帮助我们对代码做`polyfill`，还可以**编译转换**诸如`.less, .vue, .jsx`这类在浏览器**无法识别的格式文件**，让我们在开发的时候可以使用新特性和新语法做开发，提高开发效率。

- **能力扩展。**

通过`webpack`的`Plugin`机制，我们在实现模块化打包和编译兼容的基础上，可以**进一步实现诸如按需加载，代码压缩等一系列功能**，帮助我们进一步**提高自动化程度**，工程效率以及打包输出的质量。

## webpack打包流程

- 1、读取`webpack`的配置参数；
- 2、启动`webpack`，创建`Compiler`对象并开始解析项目；
- 3、从入口文件（`entry`）开始解析，并且找到其导入的依赖模块，递归遍历分析，形成依赖关系树；
- 4、对不同文件类型的依赖模块文件使用对应的`Loader`进行编译，最终转为`Javascript`文件；
- 5、整个过程中`webpack`会通过发布订阅模式，向外抛出一些`hooks`，而`webpack`的插件即可通过监听这些关键的事件节点，执行插件任务进而达到干预输出结果的目的。

其中文件的解析与构建是一个比较复杂的过程，在`webpack`源码中主要依赖于`compiler`和`compilation`两个核心对象实现。

`compiler`对象是一个**全局单例**，他负责把控整个`webpack`打包的构建流程。 `compilation`对象是每一次构建的上下文对象，它包含了当次构建所需要的所有信息，**每次热更新和重新构建，`compiler`都会重新生成一个新的`compilation`对象**，负责此次更新的构建过程。

而每个**模块间的依赖关系，则依赖于`AST`语法树**。每个模块文件在通过`Loader`解析完成之后，会通过`acorn`库生成模块代码的`AST`语法树，通过语法树就可以分析这个模块是否还有依赖的模块，进而继续循环执行下一个模块的编译解析。

**最终`Webpack`打包出来的`bundle`文件是一个`IIFE`的执行函数。**（立即执行函数）

## sourceMap是什么？

`sourceMap`是一项**编译、打包、压缩后的代码映射回源代码**的技术

由于打包压缩后的代码并没有阅读性可言，一旦在开发中报错或者遇到问题，直接在混淆代码中`debug`问题会带来非常糟糕的体验，`sourceMap`可以帮助我们快速定位到源代码的位置，提高我们的开发效率

## 是否写过loader?说一下思路

`Webpack`最后打包出来的成果是一份`Javascript`代码，实际上在`Webpack`内部默认也只能够处理`JS`模块代码

在打包过程中，会默认把所有遇到的文件都当作 `JavaScript`代码进行解析，因此当项目存在非`JS`类型文件时，我们需要先对其进行必要的转换，才能继续执行打包任务，这也是`Loader`机制存在的意义。



