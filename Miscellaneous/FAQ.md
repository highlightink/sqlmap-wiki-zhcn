# 常见问题

> *译自：[FAQ](https://github.com/sqlmapproject/sqlmap/wiki/FAQ)*

## 什么是 sqlmap？

sqlmap 是一款开源渗透测试工具，它能自动检测并利用 SQL 注入漏洞和接管数据库服务器。它具有强大的检测引擎、许多适用于最终渗透测试阶段的特色功能以及范围广泛的开关，涵盖了数据库指纹识别、从数据库获取数据、访问底层文件系统以及通过带内连接在操作系统上执行命令。

## 如何运行 sqlmap?

如果是在 UNIX/Linux 上，请在终端中输入以下命令：

    python sqlmap.py -h

你还可以输入以下命令以查看详细的帮助信息：

    python sqlmap.py -hh

如果是在 Windows 系统上，请在终端中输入以下命令：

    C:\PythonXX\python.exe sqlmap.py -h

`C:\PythonXX` 是 [Python](http://www.python.org) 的安装路径，Python 的版本应满足 **2.6**, **2.7** 或 **3.x**。

## 我可以将 sqlmap 集成到我正在开发的安全工具中吗？

**可以**。sqlmap 遵照 [GPLv2](http://www.gnu.org/licenses/gpl-2.0.html) 发布，这意味着任何的衍生作品不能使用比通用公共许可证本身更严格的协议发布。

## 我可以将 sqlmap 嵌入到专有软件中吗？

如果你希望在专有软件中嵌入 sqlmap 技术，请遵照我们使用的另一个许可证（联系 [sales@sqlmap.org](sales@sqlmap.org)）。

## 如何报告 bugs 或请求新特性？

**欢迎报告 Bug**！
请在 [issue tracker](https://github.com/sqlmapproject/sqlmap/issues) 报告所有 bugs，或者发送到[邮件列表](https://lists.sourceforge.net/lists/listinfo/sqlmap-users)。

指引：

* 在提交错误报告之前，请先查找开放和已关闭的 issues，确保是未被报告过的问题。此外，请查看[用户手册](https://github.com/sqlmapproject/sqlmap/wiki)以获取任何相关内容。
* 确保该错误可以在 sqlmap 的最新开发版本中重现。
* 你的报告应提供如何重现问题的详细说明。如果 sqlmap 抛出未处理异常，则需要错误回溯信息。也欢迎报告非预期行为的细节。最好是提供一个简单的测试用例（几行）。
* 如果你正提出功能增强请求，请列出你请求该功能的理由。*为什么这个功能会很有用？*
* 如果你不确定某些情况是否为 bug，或者想在发出功能增强请求之前讨论某个潜在的新功能，那么[邮件列表](https://lists.sourceforge.net/lists/listinfo/sqlmap-users)会是一个很好的地方。

## 我能时常参与开发吗？

非常感谢所有的代码贡献。首先，克隆 [Git 仓库](https://github.com/sqlmapproject/sqlmap)，并仔细地阅读[用户手册](https://github.com/sqlmapproject/sqlmap/wiki)，尝试自己阅读源码，如果你在把握 sqlmap 的架构和意义时出现困难，请[给我们发邮件](mailto:dev@sqlmap.org)。我们为不充分的代码注释感到抱歉——你可以通读它并[改进它](https://github.com/sqlmapproject/sqlmap/issues/37)。

我们首选的提交补丁方法是通过 Git [pull request](https://help.github.com/articles/using-pull-requests)。许多[人](https://raw.github.com/sqlmapproject/sqlmap/master/doc/THANKS.md)以不同的方式为 sqlmap 开发做出了贡献。**你**可以是下一个！

为了保持所有代码的一致性和可读性，我们希望你遵守以下说明：

* 每个补丁有一个逻辑修改。
* 尽可能使代码保持每行不超过 76 列。
* 避免使用制表符，使用四个空格代替。
* 在你花时间在一个重大补丁之前，值得在[邮件列表](https://lists.sourceforge.net/lists/listinfo/sqlmap-users)中或者私下发送[邮件](mailto:dev@sqlmap.org)讨论它。
* 不要在一次 pull request 中改变多个文件的代码风格，我们可以在进行任何重大修改之前[讨论](mailto:dev@sqlmap.org)，但请注意，不被 [PEP 8](http://www.python.org/dev/peps/pep-0008/) 强烈建议的个人偏好很可能会被拒绝。
* 在每次 pull request 中对少于五个文件进行更改——很少有好的理由在一次 pull request 中修改五个以上的文件，因为这大大增加了落实（提交）这些 pull requests 所需的审阅时间。
* 与 master 分支相差太多的风格将被开发者进行适当“调整”。
* 不要对 `thirdparty/` 和 `extra/` 目录中的任何内容进行修改。

通过向 sqlmap 开发人员、邮件列表或通过 Git pull request 贡献代码，将它们收入sqlmap 源代码仓库，你需要了解（除非另有说明）的是这代表你提供给了 sqlmap 项目无限制、非独占的权利来对它进行重用、修改和再次授权。虽然 sqlmap 会一直保持开源，但这很重要，因为无法重新授权代码已经为其他自由软件项目（如 KDE 和 NASM）带来了毁灭性的问题。如果你想为你的贡献指定特殊的许可条件，请在发送时说明。

## 我可以参与到长期的开发活动中吗？

我们一直在寻求能够编写高质量 Python 代码、想要做安全研究，懂 Web 应用安全、数据库访问和接管、软件重构并且积极想加入开发团队的人。

如果你对此感兴趣，请向我们发起 [pull requests](https://help.github.com/articles/using-pull-requests)——我们对主仓库的推送权限保持开放[讨论](mailto:dev@sqlmap.org)，如果你能证明自己的专业性、积极性和编写合格 Python 代码的能力。

## 我该如何支持、感谢项目的开发活动？

sqlmap 是由一个计算机安全爱好者组成的小团队投入大量时间与热情凝结而成的。如果你欣赏我们的工作，并且希望 sqlmap 能够持续改进，可以考虑通过 [PayPal](https://www.paypal.com) 向 `donations@sqlmap.org` 发起一次捐赠。

## 我该如何跟进开发活动？

随着开发时间的推移，我们倾向于维护我们的 Twitter 主页，[@sqlmap](https://twitter.com/sqlmap)。我们会比[邮件列表](http://news.gmane.org/gmane.comp.security.sqlmap)更频繁地更新它。因此，如果你希望近距离了解开发活动，你可以：

* 在 GitHub [查看](https://github.com/sqlmapproject/sqlmap/toggle_watch)（译者注：链接已失效）本项目。
* 使用你的 Feed 阅读器订阅 [Atom feed](https://github.com/sqlmapproject/sqlmap/commits/master.atom)。
* 在 Twitter 关注我们，[@sqlmap](https://twitter.com/sqlmap)。
* 在 YouTube 观看演示：[#1](http://www.youtube.com/user/inquisb/videos) 和 [#2](http://www.youtube.com/user/stamparm/videos)。
* 订阅[邮件列表](http://news.gmane.org/gmane.comp.security.sqlmap)。
  * 也可以订阅 [RSS feed](http://rss.gmane.org/messages/complete/gmane.comp.security.sqlmap)。
  * 还可以在线浏览[文章归档](http://news.gmane.org/gmane.comp.security.sqlmap)。

## 你能帮我黑掉一个网站吗？

**不能**。

## 工具 `xyz` 对目标有用而 sqlmap 不行！

请使用其他工具。

## 哪个篡改脚本可以用来绕过（WAF/IPS）保护？

如果你无法手动评估目标，请勿使用篡改脚本。篡改脚本仅在渗透测试人员首先（最可能是在几个小时的请求/响应检查之后）了解如何绕过防护的情况下使用。在不充分理解的情况下盲目使用和组合多种篡改脚本总是一个坏主意。

## 我的网站被 sqlmap 攻击了。别再开发它了*王八蛋*！？

我们偶尔会收到不明人士的愤怒。应该强调的是，以下提示消息，**每次运行 sqlmap 的终端用户都负有义务**：

    [!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent
    is illegal. It is the end user's responsibility to obey all applicable local, state and
    federal laws. Developers assume no liability and are not responsible for any misuse or
    damage caused by this program

## sqlmap 什么时候会切换到 Python 3？

~~目前，sqlmap 项目没有切换到新版本 Python 解释器的需要，因为转换过程对于较大的项目可能很麻烦（由于向后的不兼容性）。Python 版本的切换是早晚的事情，但目前它是一个[优先级非常低的任务](https://github.com/sqlmapproject/sqlmap/issues/93)。~~

已于 2019 年五月同时支持 Python 2 与 3。

## 如何缩短 sqlmap 注入的 payloads？

你可以使用 sqlmap 的以下开关：

    --no-cast           关闭 payload 构造机制

然而另一方面，你可能会失去此开关在默认配置中提供的好处。

## `WARNING unknown charset '...'` 是什么意思？

sqlmap 需要正确解码页面内容才能正确检测和处理国际化字符。在某些情况下，Web 开发人员在声明网页字符编码时犯错（例如：使用了 `iso_8859` 而不是标准化名称 `iso-8859`），这会导致问题的出现。作为一个故障安全机制，我们已经引入了启发式检测引擎 [chardet](http://chardet.feedparser.org/)，所以在大多数情况下，sqlmap 会自动处理这种问题。

不过，强烈建议你向我们报告这些打印*错误*，以便我们可以在代码中手动处理这些错误。

问题：
[#1](http://thread.gmane.org/gmane.comp.security.sqlmap/737)
[#2](http://thread.gmane.org/gmane.comp.security.sqlmap/1232)
[#3](http://thread.gmane.org/gmane.comp.security.sqlmap/1239)

## 如何使用 sqlmap 并开启 `mod_rewrite` ？

在 sqlmap 应该检查有无注入点的 URI 的对应位置加入星号，即 `*`。例如，对于 `./sqlmap.py -u "http://target.tld/id1/1*/id2/2"`，sqlmap 会在标注了 `*` 号的地方尝试注入 payloads。

此功能也适用于 POST 数据。支持多个注入点，并会依次对这些注入点进行评估。

选项：
[#1](http://thread.gmane.org/gmane.comp.security.sqlmap/731)
[#2](http://thread.gmane.org/gmane.comp.security.sqlmap/728)
[#3](http://thread.gmane.org/gmane.comp.security.sqlmap/1258)

## 为什么 sqlmap 有时无法获取密码哈希？

会话用户很有可能没有足够的权限来对包含密码哈希的系统表进行查询。

问题：
[#1](http://thread.gmane.org/gmane.comp.security.sqlmap/714)

## 开关 `--text-only` 是干嘛用的？

开关 `--text-only` 用来移除所获取页面中的非文本数据（标签、JavaScript 代码，样式等），以进一步提高 SQL 注入检测功能。

问题：
[#1](http://thread.gmane.org/gmane.comp.security.sqlmap/699)

## 我收到了 `[CRITICAL] connection timed` 的提示，但是我能正常地浏览那个网站？

有些 WAF/IPS 根据 sqlmap 的默认 `User-Agent` HTTP 头部（例如：`User-agent: sqlmap/1.0-dev`）将 sqlmap 发出的所有请求过滤掉。为了避免这种情况，建议你使用 `--random-agent` 开关。

如果你对所有目标都收到了这类提示消息，那你很可能需要重新设置下你的代理（开关：`--proxy` 和/或 `--ignore-proxy`）。

问题：
[#1](http://thread.gmane.org/gmane.comp.security.sqlmap/1241)

## 可以通过 `--sql-query`，`--sql-shell` 和 `--sql-file` 使用 SQL 的 `INSERT/UPDATE` 命令吗？

当目标应用程序存在堆叠查询（Stacked queries） SQL 注入漏洞，或者使用 `-d` 开关直接连接到数据库，并且会话用户具有相应的权限（或提权工具已经被预先注入），便可以在目标数据库上运行这些语句以及任何其他语句。

问题：
[#1](http://thread.gmane.org/gmane.comp.security.sqlmap/1237)

## 为什么 sqlmap 不能检测/利用注入而其他商业工具能？

在大多数这些情况中，商业工具检测使用公然的错误信息导致*假阳性*结果。你必须注意的是，DBMS 错误消息并不意味着受影响的 Web 应用程序容易受到 SQL 注入攻击。sqlmap 会进一步执行几个步骤，并且从来不会在没有通过测试时通报一个注入点，如果它在一开始就可以被利用。

问题：
[#1](http://thread.gmane.org/gmane.comp.security.sqlmap/970)

## 如何基于我的条件导出数据表的特定条目？

sqlmap 在从表中导出数据条目方面非常细致。相关开关为：

    --dump              导出 DBMS 数据库表项
    -D DB               指定要枚举的 DBMS 数据库
    -T TBL              指定要枚举的 DBMS 数据表
    -C COL              指定要枚举的 DBMS 数据列
    --start=LIMITSTART  指定获取返回查询结果的开始行数
    --stop=LIMITSTOP    指定获取返回查询结果的结束行数
    --first=FIRSTCHAR   指定获取返回查询结果的开始字符位
    --last=LASTCHAR     指定获取返回查询结果的结束字符位

但是，在某些情况下，你可能需要使用自定义的 `WHERE` 条件语句来导出所有数据条目。对于这种情况，我们建议你使用以下的开关：

    --sql-query=QUERY   指定要执行的 SQL 语句
    --sql-shell         调出交互式 SQL shell
    --sql-file=SQLFILE  执行文件中的 SQL 语句

例如：

    --sql-query "SELECT user, password FROM users WHERE privilege='admin'"

问题：
[#1](http://thread.gmane.org/gmane.comp.security.sqlmap/2309)

## 哪里可以找到旧版本的 sqlmap？

在 GitHub 的[标签](https://github.com/sqlmapproject/sqlmap/tags)页面中。

问题：
[#1](http://thread.gmane.org/gmane.comp.security.sqlmap/2290)
