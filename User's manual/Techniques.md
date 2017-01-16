# 技术

sqlmap 能够用于检测和利用五种不同**类型**的 SQL 注入。

* **布尔型盲注**：sqlmap 可以通过替换或者添加相关的 SQL 语句到 HTTP 请求的查询参数里面，相关的 SQL 语句可能是合法的 `SELECT` 子查询，也可能是任意用于获取输出数据的 SQL 语句。针对每一个注入检测的 HTTP 响应，sqlmap 会对比原先请求响应的头部／主体，相关的工具会对注入结果进行逐个字符对比。或者，用户可以预先提供字符串或正则表达式，用于对正确页面结果进行对应的匹配。sqlmap 内部实现了二分算法，并且充分利用了 HTTP 七种不同请求方法，能够快速地匹配对应响应内容。如果请求响应结果不是简单的明文字符集，sqlmap 则会采取其它算法对输出结果进行检测。
* **时间型盲注**：sqlmap 可以通过替换或者添加相关的 SQL 语句到 HTTP 请求的查询参数里面，相关的 SQL 语句可能是合法的、用于使后端数据库延迟几秒响应的相关查询。针对每一个注入检测的 HTTP 响应，sqlmap 会纪录并对比原先请求响应的时间，相关的工具也会对注入输出结果进行逐个字节对比。正如基于布尔值盲注技术一样，二分算法也会被应用。
* **报错型注入**：sqlmap 可以通过替换或者添加用于引发特定数据库错误的 SQL 语句到查询参数里面，并通过解析对应的注入结果，看特定的数据库错误信息是否存在响应的头部／主体中。这项技术只有在 Web 应用配置开启后端数据库错误信息提醒才有效。
* **联合查询注入**：sqlmap 可以在相关查询参数中添加以 `UNION ALL SELECT` 开头的合法 SQL 语句。当 Web 应用中直接使用 `for` 循环对 `SELECT` 的查询结果进行输出，或者采用相同的策略将执行结果在页面中逐行输出时，这项技术则会生效。就算查询结果不使用 `for` 循环进行全部输出，只输出前面的首个元素，sqlmap 还是能够利用 **局部（单点）联合查询 SQL 注入**技术进行漏洞挖掘，
* **堆叠查询注入**, 也就是 **捎带查询**: sqlmap 会测试 Web 应用是否支持堆叠查询，如果支持，则在 HTTP 请求的查询参数中添加一个分号 (`;`)，并在后面加上注入的 SQL 语句。这项技术不仅适用于执行 `SELECT` 语句，同样适用于执行**数据定义** 或者 **数据操作**等 SQL 语句，同时可能可以获取到文件系统相关的读写权限和系统命令执行权限，不过这很大程度上取决于底层数据库管理系统和当前会话用户权限。
---
* **UNION query-based**: sqlmap appends to the affected parameter a syntactically valid SQL statement starting with an `UNION ALL SELECT`. This techique works when the web application page passes directly the output of the `SELECT` statement within a `for` loop, or similar, so that each line of the query output is printed on the page content. sqlmap is also able to exploit **partial (single entry) UNION query SQL injection** vulnerabilities which occur when the output of the statement is not cycled in a `for` construct, whereas only the first entry of the query output is displayed.
* **Stacked queries**, also known as **piggy backing**: sqlmap tests if the web application supports stacked queries and then, in case it does support, it appends to the affected
parameter in the HTTP request, a semi-colon (`;`) followed by the SQL statement to be executed. This technique is useful to run SQL statements other than `SELECT`, like for instance, **data definition** or **data manipulation** statements, possibly leading to file system read and write access and operating system command execution depending on the underlying back-end database management system and the session user privileges.
