# 技术

> *译自：[Techniques](https://github.com/sqlmapproject/sqlmap/wiki/Techniques)*

sqlmap 可用于检测利用五种不同**类型**的 SQL 注入。

* **布尔型盲注（Boolean-based blind）**：sqlmap 会替换或添加 SQL 语句到 HTTP 请求的查询参数里面，相关的 SQL 语句可能是合法的 `SELECT` 子查询，也可以是任意用于获取输出数据的 SQL 语句。针对每个注入检测的 HTTP 响应，sqlmap 通过对比原始请求响应的 headers/body，从而逐个字符地推导出注入语句的输出。或者，用户可以预先提供一个字符串或正则表达式，用于对正确页面结果进行匹配。sqlmap 内部实现了二分算法，使得输出中的每一个字符可在最多 7 个 HTTP 请求内被获取。如果请求响应结果不是简单的明文字符集，sqlmap 会采取更大范围的算法来检测输出。
* **时间型盲注（Time-based blind）**：sqlmap 会替换或者添加相关的 SQL 语句到 HTTP 请求的查询参数里面，相关的 SQL 语句可能是合法的、用于使后端 DBMS（Database Management System，数据库管理系统）延迟几秒响应的查询。针对每一个注入检测的 HTTP 响应，通过对 HTTP 响应时间与原始请求之间进行比较，从而逐个字符地推导出注入语句的输出。正如基于布尔型盲注的技术一样，二分算法也会被应用。
* **报错型注入（Error-based）**：sqlmap 会替换或者添加用于引发特定数据库错误的 SQL 语句到查询参数里面，并通过解析对应的注入结果，判断特定的数据库错误信息是否存在响应的 headers/body 中。这项技术只有在 Web 应用配置开启后端 DBMS 错误信息提醒才有效。
* **联合查询注入（UNION query-based）**：sqlmap 会在查询参数中添加以 `UNION ALL SELECT` 开头的合法 SQL 语句。当 Web 应用在 `for` 循环中直接传递 `SELECT` 语句的查询结果，或采用了类似的方法将查询结果在页面中逐行输出时，这项技术会生效。当查询结果不使用 `for` 循环进行全部输出而只输出首个结果，sqlmap 还能够利用**偏（单入口）联合查询 SQL 注入**漏洞。
* **堆叠查询注入（Stacked queries）**，也被称为**捎带查询注入（piggy backing）**：sqlmap 会测试 Web 应用是否支持堆叠查询，如果支持，则在 HTTP 请求的查询参数中添加一个分号（`;`），并在后面加上注入的 SQL 语句。这项技术不仅适用于执行 `SELECT` 语句，同样适用于执行**数据定义**或者**数据操作**等 SQL 语句，同时可能可以获取到文件系统的读写权限和系统命令执行权限，不过这很大程度上取决于底层 DBMS 和当前会话用户的权限。
