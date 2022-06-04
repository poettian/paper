#### go build

When compiling multiple packages or a single non-main package,build compiles the packages but discards the resulting object,serving only as a check that the packages can be built.

当构建库包时，会对库包进行编译但不保存编译结果文件。

这样的话对库包进行构建的结果就是：仅仅做了一个检查，确定库包能够被正常构建。

#### go install

When module-aware mode is disabled, other packages are installed in the
directory `$GOPATH/pkg/$GOOS_$GOARCH`. When module-aware mode is enabled,
other packages are built and cached but not installed.

当关闭 `go modules` 时，安装库包会将构建过的归档文件(.a 结尾的静态库文件)放到 `GOPATH` 的相关目录下。

通过 `go env` 可以知道：

`GOOS="darwin"`

`GOARCH="amd64"`

当开启 `go modules` 时，安装库包只会进行构建并将结果放入缓存中。

