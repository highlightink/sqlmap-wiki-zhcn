## 优化

下面的开关可以用于优化 sqlmap 的性能表现。

### 批量优化

开关: `-o`

设置这个开关表示隐含开启下面对应的选项和开关：

* `--keep-alive`
* `--null-connection`
* `--threads=3` 默认值，可以设置更高参数。

查看下面内容获取更多关于开关设置的详情。

### 输出预测

开关: `--predict-output`

这个开关设置用于推导性算法，可对获取的数据特性进行线性数据分析预测。
This switch is used in inference algorithm for sequential statistical prediction of characters of value being retrieved. Statistical table with the most promising character values is being built based on items given in `txt/common-outputs.txt` combined with the knowledge of current enumeration used. In case that the value can be found among the common output values, as the process progresses, subsequent character tables are being narrowed more and more. If used in combination with retrieval of common DBMS entities, as with system table names and privileges, speed up is significant. Of course, you can edit the common outputs file according to your needs if, for instance, you notice common patterns in database table names or similar.

This switch is used in inference algorithm for sequential statistical prediction of characters of value being retrieved. Statistical table with the most promising character values is being built based on items given in `txt/common-outputs.txt` combined with the knowledge of current enumeration used. In case that the value can be found among the common output values, as the process progresses, subsequent character tables are being narrowed more and more. If used in combination with retrieval of common DBMS entities, as with system table names and privileges, speed up is significant. Of course, you can edit the common outputs file according to your needs if, for instance, you notice common patterns in database table names or similar.

值得注意的是， 这个开关不能够和 `--threads` 一起使用。

### HTTP Keep-Alive

开关: `--keep-alive`

这个开关参数设置 sqlmap 使用 HTTP(s) 持久化连接。

值得注意的是，这个开关不能够和 `--proxy` 一起使用。

### HTTP NULL 连接

开关: `--null-connection`

在 HTTP 请求中，存在可以获取 HTTP 响应的大小而无须获取整个 HTTP 实体。这个技术可用于 SQL 盲注中，通过返回的 `True` 和 `False` 来筛选可进行注入的请求。如果开启了这个开关， sqlmap 会使用两种不同的 NULL 连接技术：`Range`  和 `HEAD`， 用于测试和渗透目标。如果目标服务器能够满足其中之一的请求方式，那将能够减小带宽通讯，加速整个入侵过程。

这些技术的相关详情可见白皮书 [提升 SQL 盲注的性能 - Take 2 (带宽)](http://www.wisec.it/sectou.php?id=472f952d79293).

值得注意的是，这个开关不能够和 `--text-only` 一起使用。

### 并发 HTTP(S) 请求

选项: `--threads`

sqlmap 中支持设定最大并发 HTTP(S) 请求。 
这个特性依赖于 [多线程](http://en.wikipedia.org/wiki/Multithreading), 因而继承了多线程的优点和缺陷。

当数据是通过 SQL 盲注技术获取，或者使用蛮力参数配置获取，可以运用这个特性。对于 SQL 盲注技术，sqlmap 首先在单线程计算出查询结果的长度，然后启用多线程特性，为每一个线程分配查询结果的一个字符。当查询结果的所有字符获取完毕后，线程则结束退出，结合 sqlmap 中实现的折半算法，这个过程最多发起 7次 HTTP(S) 请求。

为了运行性能和目标站点的可靠性因素考虑，sqlmap 最大的并发请求数只能设置到 10.

值得注意的是，这个选项不能跟 `--predict-output` 一起使用。
