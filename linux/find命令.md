#### -exec

If you run `find` with `exec`, `{}` expands to the filename of each file or directory found with `find` (so that `ls` in your example gets every found filename as an argument - note that it calls `ls` or whatever other command you specify once for each file found).

Semicolon `;` ends the command executed by `exec`. It needs to be escaped with `\` so that the shell you run `find` inside does not treat it as its own special character, but rather passes it to `find`.

See [this article](http://web.archive.org/web/20130204072017/http://content.hccfl.edu/pollock/unix/findcmd.htm) for some more details.

------

Also, `find` provides some optimization with `exec cmd {} +` - when run like that, `find` appends found files to the end of the command rather than invoking it once per file (so that the command is run only once, if possible).

The difference in behavior (if not in efficiency) is easily noticeable if run with `ls`, e.g.

```
find ~ -iname '*.jpg' -exec ls {} \;
# vs
find ~ -iname '*.jpg' -exec ls {} +
```

Assuming you have some `jpg` files (with *short enough paths*), the result is one line per file in first case and standard `ls` behavior of displaying files in columns for the latter.