##### 开启session

```shell
screen -S sockname
```

##### session窗口

To create a new window with shell type `Ctrl+a` `c`, the first available number from the range `0...9` will be assigned to it.

- `Ctrl+a` `c` Create a new window (with shell).
- `Ctrl+a` `"` List all windows.
- `Ctrl+a` `S` Split current region horizontally into two regions.
- `Ctrl+a` `|` Split current region vertically into two regions.

##### detach session

`Ctrl+a` `d`

##### reattach session

```shell
screen -r session.id
```

##### 关闭session

```she
screen -X -S [session # you want to kill] quit
```

