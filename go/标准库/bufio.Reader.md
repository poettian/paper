##### ReadSlice、ReadBytes、ReadString 和 ReadLine 方法

以下内容摘自[《Go语言标准库》](https://raw.githubusercontent.com/polaris1119/The-Golang-Standard-Library-by-Example/master/chapter01/01.4.md)：

之所以将这几个方法放在一起，是因为他们有着类似的行为。事实上，后三个方法最终都是调用ReadSlice来实现的。所以，我们先来看看ReadSlice方法。(感觉这一段直接看源码较好)

**ReadSlice方法签名**如下：

```go
	func (b *Reader) ReadSlice(delim byte) (line []byte, err error)
```
ReadSlice 从输入中读取，直到遇到第一个界定符（delim）为止，返回一个指向缓存中字节的 slice，在下次调用读操作（read）时，这些字节会无效。举例说明：
```go
	reader := bufio.NewReader(strings.NewReader("http://studygolang.com. \nIt is the home of gophers"))
	line, _ := reader.ReadSlice('\n')
	fmt.Printf("the line:%s\n", line)
	// 这里可以换上任意的 bufio 的 Read/Write 操作
	n, _ := reader.ReadSlice('\n')
	fmt.Printf("the line:%s\n", line)
	fmt.Println(string(n))
```
输出：
```bash
	the line:http://studygolang.com. 
	
	the line:It is the home of gophers
	It is the home of gophers
```
从结果可以看出，第一次ReadSlice的结果（line），在第二次调用读操作后，内容发生了变化。也就是说，ReadSlice 返回的 []byte 是指向 Reader 中的 buffer ，而不是 copy 一份返回。正因为ReadSlice 返回的数据会被下次的 I/O 操作重写，因此许多的客户端会选择使用 ReadBytes 或者 ReadString 来代替。读者可以将上面代码中的 ReadSlice 改为 ReadBytes 或 ReadString ，看看结果有什么不同。

注意，这里的界定符可以是任意的字符，可以将上面代码中的'\n'改为'm'试试。同时，返回的结果是包含界定符本身的，上例中，输出结果有一空行就是'\n'本身(line携带一个'\n',printf又追加了一个'\n')。

如果 ReadSlice 在找到界定符之前遇到了 error ，它就会返回缓存中所有的数据和错误本身（经常是 io.EOF）。如果在找到界定符之前缓存已经满了，ReadSlice 会返回 bufio.ErrBufferFull 错误。当且仅当返回的结果（line）没有以界定符结束的时候，ReadSlice 返回err != nil，也就是说，如果ReadSlice 返回的结果 line 不是以界定符 delim 结尾，那么返回的 er r也一定不等于 nil（可能是bufio.ErrBufferFull或io.EOF）。
例子代码：
```go
	reader := bufio.NewReaderSize(strings.NewReader("http://studygolang.com"),16)
	line, err := reader.ReadSlice('\n')
	fmt.Printf("line:%s\terror:%s\n", line, err)
	line, err = reader.ReadSlice('\n')
	fmt.Printf("line:%s\terror:%s\n", line, err)
```
输出：
```bash
	line:http://studygola	error:bufio: buffer full
	line:ng.com	error:EOF
```
**ReadBytes方法签名**如下：
```go
	func (b *Reader) ReadBytes(delim byte) (line []byte, err error)
```
该方法的参数和返回值类型与 ReadSlice 都一样。 ReadBytes 从输入中读取直到遇到界定符（delim）为止，返回的 slice 包含了从当前到界定符的内容 **（包括界定符）**。如果 ReadBytes 在遇到界定符之前就捕获到一个错误，它会返回遇到错误之前已经读取的数据，和这个捕获到的错误（经常是 io.EOF）。跟 ReadSlice 一样，如果 ReadBytes 返回的结果 line 不是以界定符 delim 结尾，那么返回的 err 也一定不等于 nil（可能是bufio.ErrBufferFull 或 io.EOF）。

从这个说明可以看出，ReadBytes和ReadSlice功能和用法都很像，那他们有什么不同呢？

在讲解ReadSlice时说到，它返回的 []byte 是指向 Reader 中的 buffer，而不是 copy 一份返回，也正因为如此，通常我们会使用 ReadBytes 或 ReadString。很显然，ReadBytes 返回的 []byte 不会是指向 Reader 中的 buffer，通过[查看源码](http://docscn.studygolang.com/src/bufio/bufio.go?s=10277:10340#L338)可以证实这一点。

还是上面的例子，我们将 ReadSlice 改为 ReadBytes：
```go
	reader := bufio.NewReader(strings.NewReader("http://studygolang.com. \nIt is the home of gophers"))
	line, _ := reader.ReadBytes('\n')
	fmt.Printf("the line:%s\n", line)
	// 这里可以换上任意的 bufio 的 Read/Write 操作
	n, _ := reader.ReadBytes('\n')
	fmt.Printf("the line:%s\n", line)
	fmt.Println(string(n))
```
输出：
```bash
	the line:http://studygolang.com. 

	the line:http://studygolang.com. 
	
	It is the home of gophers
```
**ReadString方法**

看一下该方法的源码：
```go
	func (b *Reader) ReadString(delim byte) (line string, err error) {
		bytes, err := b.ReadBytes(delim)
		return string(bytes), err
	}
```
它调用了 ReadBytes 方法，并将结果的 []byte 转为 string 类型。

**ReadLine方法签名**如下
```go
	func (b *Reader) ReadLine() (line []byte, isPrefix bool, err error)
```
ReadLine 是一个底层的原始行读取命令。许多调用者或许会使用 ReadBytes('\n') 或者 ReadString('\n') 来代替这个方法。

ReadLine 尝试返回单独的行，不包括行尾的换行符。如果一行大于缓存，isPrefix 会被设置为 true，同时返回该行的开始部分（等于缓存大小的部分）。该行剩余的部分就会在下次调用的时候返回。当下次调用返回该行剩余部分时，isPrefix 将会是 false 。跟 ReadSlice 一样，返回的 line 只是 buffer 的引用，在下次执行IO操作时，line 会无效。可以将 ReadSlice 中的例子该为 ReadLine 试试。

注意，返回值中，要么 line 不是 nil，要么 err 非 nil，两者不会同时非 nil。

ReadLine 返回的文本不会包含行结尾（"\r\n"或者"\n"）。如果输入中没有行尾标识符，不会返回任何指示或者错误。

从上面的讲解中，我们知道，读取一行，通常会选择 ReadBytes 或 ReadString。不过，正常人的思维，应该用 ReadLine，只是不明白为啥 ReadLine 的实现不是通过 ReadBytes，然后清除掉行尾的\\n（或\r\n），它现在的实现，用不好会出现意想不到的问题，比如丢数据。个人建议可以这么实现读取一行：
```go
	line, err := reader.ReadBytes('\n')
	line = bytes.TrimRight(line, "\r\n")
```
这样既读取了一行，也去掉了行尾结束符（当然，如果你希望留下行尾结束符，只用ReadBytes即可）。

##### 参考文章

[bufio — 缓存IO](https://books.studygolang.com/The-Golang-Standard-Library-by-Example/chapter01/01.4.html)