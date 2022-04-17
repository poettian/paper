#### 什么是SCL

> [Software Collections](https://www.softwarecollections.org/en/) , also known as SCL is a community project that allows you to build, install, and use multiple versions of software on the same system, without affecting system default packages. By enabling Software Collections, you gain access to the newer versions of programming languages and services which are not available in the core repositories. 
>
> The SCL repositories provide a package named Developer Toolset, which includes newer versions of the GNU Compiler Collection, and other development and debugging tools.

#### 如何安装

有多个版本可以选择：

- Developer Toolset 7
- Developer Toolset 6

```shell
sudo yum install centos-release-scl
sudo yum install devtoolset-7
```

#### 如何使用

```shell
scl enable devtoolset-7 bash
```

注意：这其实是新开了一个bash子进程

