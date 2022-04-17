##### set：初始化位置参数

调用 set 接一个或多个参数时，set 会把参数的值赋予位置参数，从 $1 开始赋值。如下例子：

```
$ cat set-it.sh
#!/bin/bash
set first second third
echo $3 $2 $1

$ ./set-it.sh
third second first
```

如上，在执行 `set-it.sh` 脚本时并没有输入参数，但是使用 set 指令后会对位置参数进行赋值。

