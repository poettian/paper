##### 作用

> Essentially just a `[]byte` with some methods to make it usable with other components. Like, you can write to it (which appends to the slice) and you can read from it (which returns data from it). Think of it as an in-memory file.

> It's just a handy wrapper around byte slice, with `io.Reader`/ `io.Writer` (and a lot of others) interfaces implemented.

> A byte buffer is used to improve performance when writing a stream of data. Rather than writing data every time you can, you wait until you build up to a designated buffer size and then flush it all out at once. This is useful for things like writing to disk where it is inefficient to write small chunks of data.