## 输出详细等级

选项：`-v`

该选项用于设置输出信息的详细等级。共有**七个**级别。默认级别为 **1**，输出包括普通信息，警告，错误，关键信息和 Python 回溯信息（如果有的话）。

* **0**: 只输出 Python 回溯信息，错误和关键信息。
* **1**: 增加输出普通信息和警告信息。
* **2**: 增加输出调试信息。
* **3**: 增加输出已注入的 payloads。
* **4**: 增加输出 HTTP 请求。
* **5**: 增加输出 HTTP 响应头
* **6**: 增加输出 HTTP 响应内容。

使用等级 **2** 能更好地了解 sqlmap 做了什么，特别是在检测阶段和使用接管功能时。如果你想知道 sqlmap 使用了什么 SQL payloads，等级 **3** 是最佳选择。在你为开发者提供潜在的 Bug 报告时，推荐使用这个等级，同时确保随标准输出一起发送使用选项 `-t` 生成的流量日志文件。
需要更深入地检测潜在 Bugs 或应对未知情况时，推荐使用 **4** 或以上等级。应当注意，还可以使用该选项的较短版本来设置详细等级，其中提供的开关（而不是选项）用字母 `v` 的个数来确定详细等级（例：用 `-v ` 代替 `-v 2`，用 `-vv` 代替 `-v 3`，用 `-vvv` 代替 `-v 4`，依此类推）。

---

## Output verbosity

Option: `-v`

This option can be used to set the verbosity level of output messages. There exist **seven** levels of verbosity. The default level is **1** in which information, warning, error, critical messages and Python tracebacks (if any occur) are displayed.

* **0**: Show only Python tracebacks, error and critical messages.
* **1**: Show also information and warning messages.
* **2**: Show also debug messages.
* **3**: Show also payloads injected.
* **4**: Show also HTTP requests.
* **5**: Show also HTTP responses' headers.
* **6**: Show also HTTP responses' page content.

A reasonable level of verbosity to further understand what sqlmap does under the hood is level **2**, primarily for the detection phase and the take-over functionalities. Whereas if you want to see the SQL payloads the tools sends, level **3** is your best choice. This level is also recommended to be used when you feed the developers with a potential bug report, make sure you send along with the standard output the traffic log file generated with option `-t`.
In order to further debug potential bugs or unexpected behaviours, we recommend you to set the verbosity to level **4** or above. It should be noted that there is also a possibility to set the verbosity by using the shorter version of this option where number of letters `v` inside the provided switch (instead of option) determines the verbosity level (e.g. `-v` instead of `-v 2`, `-vv` instead of `-v 3`, `-vvv` instead of `-v 4`, etc.)