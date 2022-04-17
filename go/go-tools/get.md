`usage: go get [-d] [-t] [-u] [-v] [-insecure] [build flags] [packages]`

第一步：解析依赖的版本

默认情况下 get 命令按照以下顺序查找：最新的 tag 版本（v0.4.5 or v1.2.3）、最新的 tag 预发布版本（v0.0.1-pre1）、最新的 commit 版本。

可以通过后缀来指定版本（go get golang.org/x/text@v0.3.0）。

也可以加前缀，但没看明白：

> The version may be a prefix: @v1 denotes the latest available version starting
> with v1.

对于存储在版本仓库中的 module，后缀版本号可以是：

> commit hash, branch identifier, or other syntax known to the source control system

如果分支名和版本选择的语法有冲突时：

> Note that branches with names that overlap with other module query syntax cannot be selected explicitly. For example, the suffix @v2 means the latest version starting with v2, not the branch named v2.

如果该依赖已经被安装，get 则会升级该依赖的版本，当然，**可以通过指定版本号来升级或是降级版本**。指定 `@none` 则会移除依赖。

其他后缀如：`@latest` `@upgrade` `@patch` 先跳过吧，也不常用。

**get 虽然默认会使用依赖的最新版本，但是不会使用最新版本的依赖的依赖，而是选择依赖所指定的版本：**

> For example, if the latest A requires module B v1.2.3, while B v1.2.4 and v1.3.1 are also available, then 'go get A' will use the latest A but then use B v1.2.3, as requested by A.

**但是使用命令行参数 `-u` 可以改变这一行为**：

> The -u flag instructs get to update modules providing dependencies of packages named on the command line to use newer minor or patch releases when available. Continuing the previous example, 'go get -u A' will use the latest A with B v1.3.1 (not B v1.2.3). If B requires module C, but C does not provide any packages needed to build packages in A (not including tests), then C will not be updated.

此外：

> In general, adding a new dependency may require upgrading existing dependencies to keep a working build, and 'go get' does this automatically. Similarly, downgrading one dependency may require downgrading other dependencies, and 'go get' does this automatically as well.

第二步：下载、构建、安装指定的包

> If an argument names a module but not a package (because there is no Go source code in the module's root directory), then the install step is skipped for that argument, instead of causing a build failure.For example 'go get golang.org/x/perf' succeeds even though there is no code corresponding to that import path.

> With no package arguments, 'go get' applies to Go package in the current directory, if any. In particular, 'go get -u' and 'go get -u=patch' update all the dependencies of that package.With no package arguments and also without -u, 'go get' is not much more than 'go install', and 'go get -d' not much more than 'go list'.

