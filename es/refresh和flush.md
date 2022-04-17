#### refresh

##### 概念

##### 特性

这是 7.0 版本的一个 **breaking changes**：

如果一个索引的分片未显示设置 `index.refresh_interval` ，那么当这个分片处于**“搜索空闲”**状态时，不会在后台进行 refresh 的操作。当搜索请求到达时，将等待直到下一次 refresh 执行(在1s内)。而带有 `wait_for_refresh` 的索引请求，会触发后台 refresh。

**“搜索空闲”** 是指当分片在 `index.search.idle.after` (默认30秒) 设置的时间内没有接收到任何搜索请求，那么该分片就会被视为”搜索空闲“。

该特性会优化处于无搜索状态时的批量索引操作。如果想跳过这个特性，只需要显示设置 `index.refresh_interval` 即可。

**个人看法**：这么来看的话，除非对**近实时搜索**没有要求，否则最好还是不要显示设置 `index.refresh_interval` 。

##### ?refresh 参数

`Index` `Update` `Delete` `Bulk` Apis 支持用 refresh 参数来控制改动数据对搜索何时可见。

有三种参数值：

* 空字符串或true

  会立刻对主分片和副本分片执行 refresh 以使改动在搜索结果中可见。

* wait_for

  在给出响应结果之前，会等待执行 refresh 。等待时间取决于 `index.refresh_interval` 的值。

  如果在等待期间，有其他对 refresh api 的调用或是 ?refresh=true 的调用，会导致结束等待直接返回。

* false

  不对refresh做任何有影响的操作，在返回后的某个时间点，改动数据对搜索结果可见。

##### 关于版本冲突



#### flush

##### 概念

flush 是一个持久化索引数据到 Lucene index 的过程，也即所谓的数据落盘。

当发生故障重启时，es 会重放事务日志，将发生故障前已经 refresh 但未落盘的数据落盘。

es 执行 flush 的触发点：

* 事务日志大小触及 `index.translog.flush_threshold_size` (默认512mb)
* es的各个shard会每个30分钟进行一次flush操作。

##### 事务日志（translog）

事务日志也需要落盘，落盘规则由下面两个参数控制：

 `index.translog.durability` 

（默认）值为 request 时，每次请求都会落盘，不存在丢失数据的风险。

值为 async 时，落盘时间间隔为 `index.translog.sync_interval` 的值，存在丢失数据的风险。

#### refresh vs flush

参考：

https://blog.csdn.net/u011686226/article/details/78531900

https://stackoverflow.com/questions/19963406/refresh-vs-flush