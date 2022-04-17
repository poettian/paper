##### bufio.Scanner 的作用

先贴一下go官方给出的解释：

> // Scanner provides a convenient interface for reading data such as
> // a file of newline-delimited lines of text. Successive calls to
> // the Scan method will step through the 'tokens' of a file, skipping
> // the bytes between the tokens. The specification of a token is
> // defined by a split function of type SplitFunc; the default split
> // function breaks the input into lines with line termination stripped. Split
> // functions are defined in this package for scanning a file into
> // lines, bytes, UTF-8-encoded runes, and space-delimited words. The
> // client may instead provide a custom split function.
> //
> // Scanning stops unrecoverably at EOF, the first I/O error, or a token too
> // large to fit in the buffer. When a scan stops, the reader may have
> // advanced arbitrarily far past the last token. Programs that need more
> // control over error handling or large tokens, or must run sequential scans
> // on a reader, should use bufio.Reader instead.
> //

大体意思就是 bufio.Scanner 提供了按照一定的分隔方式遍历 input 中 token 的能力。

这里的 `token` 是个很迷惑人的名词，其实，我们可以理解 token 就是 input 按指定分隔方式分隔后的每段数据。

那么如何设置这个分隔方式呢？这就是 `SplitFunc` 这个方法的作用。

##### bufio.Scanner 的工作方式（待验证）

既然是 bufio 包提供的能力，那么肯定就有缓冲作用其中。这个缓冲的大小，是由 bufio.Scanner.Buffer 来设置的。

```go
func (s *Scanner) Buffer(buf []byte, max int)
```

第一个参数表示起始 buffer 的大小，第二个参数表示 buffer 的最大值。

bufio.Scanner.Scan 工作时，会从 input 读取数据并写入 buffer，当 buffer 填满后，就会调用 SplitFunc。

如果在 SplitFunc 中没能拿到 token，那么其返回值 advance 就为0，Scan 就会对 buffer 扩容，继续读取 input 数据并重复上面的过程。

如果在 SplitFunc 中拿到了 token，那么 Scan 就会返回 true，通过 Text 或 Bytes 方法就能获取到这个 token 了。

##### 什么场景下使用 bufio.Scanner

> Programs that need more control over error handling or large tokens, or must run sequential scans on a reader, should use bufio.Reader instead.

大体意思就是如果需要处理更精细的错误或较大的 token 或是执行连续的扫描，应该使用 bufio.Reader 代替。

bufio.Reader 提供了更为强大的功能。

但至于具体场景下该用 bufio.Reader 还是 bufio.Scanner ，这个还有待总结。

##### 参考文章

[深入理解golang中bufio.SplitFunc](https://studygolang.com/articles/15474)