# 常见问题

## 什么是 sqlmap？

sqlmap 是一款开源渗透测试工具，它能自动检测并利用 SQL 注入漏洞和接管数据库服务器。它具有强大的检测引擎、许多适用于最终渗透测试阶段的特色功能以及范围广泛的开关，涵盖了数据库指纹识别、从数据库获取数据、访问底层文件系统以及通过带内连接在操作系统上执行命令。

## 我要如何运行 sqlmap?

如果是在 UNIX/Linux 上请在终端中输入以下命令：

    python sqlmap.py -h

你还可以输入以下命令以查看详细的帮助信息：

    python sqlmap.py -hh

如果是在 Windows 系统上请在终端中输入以下命令：

    C:\Python27\python.exe sqlmap.py -h

`C:\Python27` 是 [Python](http://www.python.org) 的安装路径，Python 的版本应满足 **>= 2.6** 和 **< 3.0**。

## 我可以将 sqlmap 集成到我正在开发的安全工具中吗？

**可以**。sqlmap 遵照 [GPLv2](http://www.gnu.org/licenses/gpl-2.0.html) 发布，这意味着任何的衍生作品不能使用比通用公共许可证本身更严格的协议发布。

## 我可以将 sqlmap 嵌入到专有软件中吗？

如果你希望在专有软件中嵌入 sqlmap 技术，请遵照我们使用的另一个许可证（联系 [sales@sqlmap.org](sales@sqlmap.org)）。

## 我如何报告 bugs 或请求新特性？

**欢迎报告 Bug**！
请在 [issue tracker](https://github.com/sqlmapproject/sqlmap/issues) 报告所有 bugs，或者发送到[邮件列表mailing list](https://lists.sourceforge.net/lists/listinfo/sqlmap-users)。

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
 * 你也可以订阅 [RSS feed](http://rss.gmane.org/messages/complete/gmane.comp.security.sqlmap)。
 * 还可以浏览[文章归档](http://news.gmane.org/gmane.comp.security.sqlmap) online。

## 你们会支持其他数据库管理系统吗？

我们已经支持所有主流和一些较小众的数据库。我们已经有计划扩大对其中一些数据库的支持，并且会某个时间点支持 Informix 和 Ingres。

## 你能帮我黑掉一个网站吗？

**不能**。

## 工具 `xyz` 对目标有用而 sqlmap 不行！

请使用其他工具。

## Which tamper script to use to bypass a (WAF/IDS/IPS) protection?

Don't use tamper scripts if you are not able to manually assess the target. Tamper scripts are used only in cases when the penetration tester knows how to bypass the protection in the first place (most probably after hours of request/response inspection). Blind usage and combination of numerous tamper scripts without the comprehension is always a bad idea.

## My site was attacked with sqlmap. Stop developing it you *dumb f.cks*!?

We get occasional rage bursts from unknown people. It should be emphasized that **with each sqlmap run end users are obligated** with the following prelude message:

    [!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent
    is illegal. It is the end user's responsibility to obey all applicable local, state and
    federal laws. Developers assume no liability and are not responsible for any misuse or
    damage caused by this program

## When sqlmap will switch to Python 3?

Currently there is no pressure on Python projects to switch to the new version of Python interpreter, as the process of switching, especially on larger projects can be cumbersome (due to the few backward incompatibilities). The switch will take place eventually, but currently it is a very [low priority task](https://github.com/sqlmapproject/sqlmap/issues/93).

## How can I shorten the payloads injected by sqlmap?

You can provide sqlmap with the following switch:

    --no-cast           Turn off payload casting mechanism

However, on the other hand you might lose the benefits provided by this switch in the default configuration.

## What does `WARNING unknown charset '...'` mean?

sqlmap needs to properly decode page content to be able to properly detect and deal with internationalized characters. In some cases web developers are doing mistakes when declaring used web page charset (e.g. `iso_8859` instead of standardized name `iso-8859`), which can cause problems. As a failsafe mechanism we have incorporated heuristic detection engine [chardet](http://chardet.feedparser.org/), so in most cases sqlmap will deal with this kind of problems automatically.
Nevertheless, you are strongly advised to report us back those typographic *mistakes* so we could handle them manually inside the code.

Question(s):
[#1](http://thread.gmane.org/gmane.comp.security.sqlmap/737)
[#2](http://thread.gmane.org/gmane.comp.security.sqlmap/1232)
[#3](http://thread.gmane.org/gmane.comp.security.sqlmap/1239)

## How to use sqlmap with `mod_rewrite` enabled?

Append an asterisk, `*`, to the place where sqlmap should check for injections in URI itself. For example, `./sqlmap.py -u "http://target.tld/id1/1*/id2/2"`, sqlmap will inject its payloads at that place marked with `*` character.
This feature also applies to POST data. Multiple injection points are supported and will be assessed sequentially.

Question(s):
[#1](http://thread.gmane.org/gmane.comp.security.sqlmap/731)
[#2](http://thread.gmane.org/gmane.comp.security.sqlmap/728)
[#3](http://thread.gmane.org/gmane.comp.security.sqlmap/1258)

## Why is sqlmap not able to get password hashes in some cases?

The session user most probably does not have enough permissions for querying on a system table containing password hashes.

Question(s):
[#1](http://thread.gmane.org/gmane.comp.security.sqlmap/714)

## What does switch `--text-only` do?

Switch `--text-only` is used for removing non-textual data (tags, javascripts, styles, etc.) from the retrieved page content to further improve SQL injection detection capabilities.

Question(s):
[#1](http://thread.gmane.org/gmane.comp.security.sqlmap/699)

## I am getting `[CRITICAL] connection timed` while I am able to browse the site normally?

There are few IDSes that filter out all sqlmap requests based on its default `User-Agent` HTTP header (e.g. `User-agent: sqlmap/1.0-dev`). To prevent this
kind of situations you are advised to use switch `--random-agent`.
If you are getting those kind of messages for all targets then you most probably need to properly set up your proxy settings (switches `--proxy`
and/or `--ignore-proxy`).

Question(s):
[#1](http://thread.gmane.org/gmane.comp.security.sqlmap/1241)

## Is it possible to use `INSERT/UPDATE` SQL commands via `--sql-query`, `--sql-shell` and `--sql-file`?

It is possible to run those statements as well as any other statement on the target database given that stacked queries SQL injection is supported by the vulnerable application or you are connecting directly to the database with `-d` switch and the session user has such privileges (or a privilege escalation vector has been injected upfront).

Question(s):
[#1](http://thread.gmane.org/gmane.comp.security.sqlmap/1237)

## sqlmap is not able to detect/exploit injection while other commercial tools are?

In most of those kind of cases blatant error message detection is used by commercial tools leading to *false positive* claims. You have to be aware that a
DBMS error message does not mean that the affected web application is vulnerable to SQL injection attacks. sqlmap goes several steps further and never claims
an injection point without making through tests if it can be exploited on the first place.

Question(s):
[#1](http://thread.gmane.org/gmane.comp.security.sqlmap/970)

## How can I dump only certain entries of a table based on my condition?

sqlmap is very granular in terms of dumping entries from a table. The relevant switches are:

    --dump              Dump DBMS database table entries
    -D DB               DBMS database to enumerate
    -T TBL              DBMS database table to enumerate
    -C COL              DBMS database table column to enumerate
    --start=LIMITSTART  First query output entry to retrieve
    --stop=LIMITSTOP    Last query output entry to retrieve
    --first=FIRSTCHAR   First query output word character to retrieve
    --last=LASTCHAR     Last query output word character to retrieve

However, in some cases you might want to dump all entries given a custom `WHERE` condition. For such cases, we recommend using one of the following switches:

    --sql-query=QUERY   SQL statement to be executed
    --sql-shell         Prompt for an interactive SQL shell
    --sql-file=SQLFILE  Execute SQL statements from given file(s)

For example:

    --sql-query "SELECT user, password FROM users WHERE privilege='admin'"

Question(s):
[#1](http://thread.gmane.org/gmane.comp.security.sqlmap/2309)

## Where can I find old versions of sqlmap?

From the [Tags](https://github.com/sqlmapproject/sqlmap/tags) page on GitHub.

Question(s):
[#1](http://thread.gmane.org/gmane.comp.security.sqlmap/2290)