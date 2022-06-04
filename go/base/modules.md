#### 什么是 main module

> The "main module" is the module containing the directory where the go command is run. The go command finds the module root by looking for a go.mod in the current directory, or else the current directory's parent directory, or else the parent's parent directory, and so on.

#### go.mod

##### 关于 indirect

>go.mod file only records the direct dependency. However, it may record an indirect dependency in the below cases：
>
>* Any indirect dependency which is not listed in the go.mod file of your direct dependency or if direct dependency doesn’t have a **go.mod** file, then that dependency will be added to the go.mod file with //indirect as the suffix
>
>* Any dependency which is not imported in any of the source files of the module.

翻译过来就是：出现 indirect 标记的两种情况

- 直接依赖模块没有使用Go Modules(也即该模块没有go.mod文件)，那么必须将该模块的间接依赖记录在主模块的 go.mod 中；
- 直接依赖 go.mod 文件不完整（没有完整记录间接依赖及其版本）；
- 主模块的源码文件中并没有引入任何依赖模块（使用 go mod tidy 命令会将这些依赖从 go.mod 中移除）；

可以通过 `go mod why -m <pkg>` 来查看依赖关系。

##### 关于 replace

通过replace将一个模块的地址转换为其它地址，用于解决某些依赖模块地址发生改变的场景。同时import命令可以无需改变(无侵入)。

##### 关于 exclude

明确排除依赖模块的某些不想导入或者有问题的版本。

**注意**

`exclude` 和 `replace` 只有在主模块的 go.mod 中才有用，Go 会忽略依赖模块 go.mod 中的这两个关键字。

##### incompatible

如果引用了一个不规范的 module（未遵循版本化语义规范），则会在 go.mod 中提示 incompatible。

#### go.sum

用于确保一致性构建。

正常情况下，go.sum 中每个依赖包版本会包含两条记录：

1）该依赖包版本整体（所有文件）的 Hash 值

2）该依赖包版本中 go.mod 文件的 Hash 值

#### 新特性

[Go 1.17新特性详解：module依赖图修剪与延迟module加载](https://tonybai.com/2021/08/19/go-module-changes-in-go-1-17/)

参考：

* [浅谈Go Modules原理](https://duyanghao.github.io/golang-module/)

