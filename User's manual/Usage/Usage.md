# 用法

```
用法：python sqlmap.py [选项]

选项：
  -h, --help            显示基本帮助信息并退出
  -hh                   显示高级帮助信息并退出
  --version             显示程序版本信息并退出
  -v VERBOSE            输出信息详细程度级别：0-6（默认为 1）

  目标：
  	至少提供一个以下选项以指定目标

    -d DIRECT           直接连接数据库
    -u URL, --url=URL   目标 URL（例："http://www.site.com/vuln.php?id=1"）
    -l LOGFILE          从 Burp 或 WebScarab 代理的日志文件中解析目标地址
    -x SITEMAPURL       从远程网站地图（.xml）文件中解析目标
    -m BULKFILE         从文本文件中获取批量目标
    -r REQUESTFILE      从文件中读取 HTTP 请求
    -g GOOGLEDORK       使用 Google dork 结果作为目标
    -c CONFIGFILE       从 INI 配置文件中加载选项

  请求：
  	以下选项可以指定连接目标地址的方式

    --method=METHOD     强制使用提供的 HTTP 方法（例：PUT）
    --data=DATA         使用 POST 发送数据串
    --param-del=PARA..  设置参数值分隔符
    --cookie=COOKIE     指定HTTP Cookie 
    --cookie-del=COO..  设置 cookie 分隔符
    --load-cookies=L..  指定以 Netscape/wget 格式存放 cookies 的文件
    --drop-set-cookie   忽略 HTTP 响应中的 Set-Cookie 
    --user-agent=AGENT  指定 HTTP User-Agent
    --random-agent      使用随机的 HTTP User-Agent
    --host=HOST         指定 HTTP Host
    --referer=REFERER   指定 HTTP Referer
    -H HEADER, --hea..  设置额外的 HTTP 头参数（例："X-Forwarded-For: 127.0.0.1"）
    --headers=HEADERS   设置额外的 HTTP 头参数（例："Accept-Language: fr\nETag: 123"）
    --auth-type=AUTH..  HTTP 认证方式（Basic，Digest，NTLM 或 PKI）
    --auth-cred=AUTH..  HTTP 认证凭证（用户名:密码）
    --auth-file=AUTH..  HTTP 认证 PEM 证书/私钥文件
    --ignore-401        忽略 HTTP 401（未授权）错误
    --proxy=PROXY       使用代理连接目标 URL
    --proxy-cred=PRO..  使用代理进行认证（用户名:密码）
    --proxy-file=PRO..  从文件中加载代理列表
    --ignore-proxy      忽略系统默认代理设置
    --tor               使用 Tor 匿名网络
    --tor-port=TORPORT  设置 Tor 代理端口代替默认端口
    --tor-type=TORTYPE  设置 Tor 代理方式（HTTP（默认），SOCKS4 或 SOCKS5）
    --check-tor         检查是否正确使用了 Tor
    --delay=DELAY       设置每个 HTTP 请求的延迟秒数
    --timeout=TIMEOUT   设置连接响应的有效秒数（默认为 30）
    --retries=RETRIES   连接超时时重试次数（默认为 3）
    --randomize=RPARAM  随机更改给定的参数值
    --safe-url=SAFEURL  测试过程中可频繁访问且合法的 URL 地址（译者注：
                        有些网站在你连续多次访问错误地址时会关闭会话连接，
                        后面的“请求”小节有详细说明）
    --safe-post=SAFE..  使用 POST 方法发送合法的数据
    --safe-req=SAFER..  从文件中加载合法的 HTTP 请求
    --safe-freq=SAFE..  每访问两次给定的合法 URL 才发送一次测试请求
    --skip-urlencode    不对 payload 数据进行 URL 编码
    --csrf-token=CSR..  设置网站用来反 CSRF 攻击的 token
    --csrf-url=CSRFURL  指定可被提取反 CSRF 攻击 token 的 URL
    --force-ssl         强制使用 SSL/HTTPS
    --hpp               使用 HTTP 参数污染攻击
    --eval=EVALCODE     在发起请求前执行给定的 Python 代码（例：
                        "import hashlib;id2=hashlib.md5(id).hexdigest()"）

  优化：
    以下选项用于优化 sqlmap 性能

    -o                  开启所有优化开关
    --predict-output    预测常用请求的输出
    --keep-alive        使用持久的 HTTP(S) 连接
    --null-connection   仅获取页面大小而非实际的 HTTP 响应
    --threads=THREADS   设置 HTTP(S) 请求并发数最大值（默认为 1）

  注入：
    以下选项用于指定要测试的参数，
    提供自定义注入 payloads 和篡改参数的脚本

    -p TESTPARAMETER    需要测试的参数
    --skip=SKIP         要跳过的参数
    --skip-static       跳过非动态参数
    --param-exclude=..  用正则表达式排除参数（例："ses"）
    --dbms=DBMS         指定 DBMS 类型（例：MySQL）
    --dbms-cred=DBMS..  DBMS 认证凭据（用户名:密码）
    --os=OS             指定 DBMS 服务器的操作系统类型
    --invalid-bignum    将无效值设置为大数
    --invalid-logical   对无效值使用逻辑运算
    --invalid-string    对无效值使用随机字符串
    --no-cast           关闭 payload 构造机制
    --no-escape         关闭字符串转义机制
    --prefix=PREFIX     注入 payload 的前缀字符串
    --suffix=SUFFIX     注入 payload 的后缀字符串
    --tamper=TAMPER     用给定脚本修改注入数据
    
  检测：
    以下选项用于自定义检测方式

    --level=LEVEL       设置测试等级（1-5，默认为 1）
    --risk=RISK         设置测试风险等级（1-3，默认为 1）
    --string=STRING     用于确定查询结果为真时的字符串
    --not-string=NOT..  用于确定查询结果为假时的字符串
    --regexp=REGEXP     用于确定查询结果为真时的正则表达式
    --code=CODE         用于确定查询结果为真时的 HTTP 状态码
    --text-only         只根据页面文本内容对比页面
    --titles            只根据页面标题对比页面

  技术：
    以下选项用于调整特定 SQL 注入技术的测试方法

    --technique=TECH    使用的 SQL 注入技术（默认为“BEUSTQ”，译者注：
                        B: Boolean-based blind SQL injection（布尔型盲注）
                        E: Error-based SQL injection（报错型注入）
                        U: UNION query SQL injection（联合查询注入）
                        S: Stacked queries SQL injection（堆查询注入）
                        T: Time-based blind SQL injection（时间型盲注）
                        Q: inline Query injection（内联查询注入）
    --time-sec=TIMESEC  延迟 DBMS 的响应秒数（默认为 5）
    --union-cols=UCOLS  设置联合查询注入测试的列数目范围
    --union-char=UCHAR  用于暴力猜解列数的字符
    --union-from=UFROM  设置联合查询注入 FROM 处用到的表
    --dns-domain=DNS..  设置用于 DNS 渗出攻击的域名（译者注：
                        推荐阅读《在SQL注入中使用DNS获取数据》
                        http://cb.drops.wiki/drops/tips-5283.html，
                        在后面的“技术”小节中也有相应解释）
    --second-order=S..  设置二阶响应的结果显示页面的 URL（译者注：
                        该选项用于二阶 SQL 注入）

  采集指纹：
    -f, --fingerprint   执行广泛的 DBMS 版本指纹采集

  枚举：
  	以下选项用于获取后端数据库管理系统的信息，结构和表里数据。
  	此外，还可以运行你输入的 SQL 语句

    -a, --all           获取所有信息、数据
    -b, --banner        获取 DBMS banner
    --current-user      获取 DBMS 当前用户
    --current-db        获取 DBMS 当前数据库
    --hostname          获取 DBMS server hostname
    --is-dba            探测 DBMS 当前用户是否为 DBA（数据库管理员，Database Administrator）
    --users             枚举出 DBMS 所有用户
    --passwords         枚举出 DBMS 所有用户的密码哈希
    --privileges        枚举出 DBMS 所有用户特权级
    --roles             枚举出 DBMS 所有用户角色
    --dbs               枚举出 DBMS 所有数据库
    --tables            枚举出 DBMS 数据库中的所有表
    --columns           枚举出 DBMS 表中的所有列
    --schema            枚举出 DBMS 所有模式
    --count             获取表数目
    --dump              转储 DBMS 数据库表项
    --dump-all          转储所有 DBMS 数据库表项
    --search            搜索列，表和/或数据库名
    --comments          获取 DBMS 注释
    -D DB               要枚举的 DBMS 数据库
    -T TBL              要枚举的 DBMS 表
    -C COL              要枚举的 DBMS 列
    -X EXCLUDECOL       枚举出所有 DBMS 列时要排除的列
    -U USER             要枚举的 DBMS 用户
    --exclude-sysdbs    枚举出所有表时排除系统数据库
    --pivot-column=P..  指定主列
    --where=DUMPWHERE   在转储表时使用 WHERE 条件语句
    --start=LIMITSTART  First query output entry to retrieve
    --stop=LIMITSTOP    Last query output entry to retrieve
    --first=FIRSTCHAR   First query output word character to retrieve
    --last=LASTCHAR     Last query output word character to retrieve
    --sql-query=QUERY   要执行的 SQL 语句
    --sql-shell         调出交互式 SQL shell
    --sql-file=SQLFILE  执行文件中的 SQL 语句

  暴力破解：
    以下选项用于暴力破解

    --common-tables     检测常用的表名
    --common-columns    检测常用的列名

  用户自定义函数注入：
    以下选项用于创建用户自定义函数

    --udf-inject        注入用户自定义函数
    --shared-lib=SHLIB  共享库的本地路径Local path of the shared library

  访问文件系统：
    以下选项用于访问后端数据库管理系统的底层文件系统
    
    --file-read=RFILE   读后端 DBMS 文件系统中的文件
    --file-write=WFILE  写后端 DBMS 文件系统中的文件
    --file-dest=DFILE   使用绝对路径写入到后端 DBMS

  访问操作系统：
    以下选项用于访问后端数据库管理系统的底层操作系统

    --os-cmd=OSCMD      执行操作系统命令
    --os-shell          调出交互式操作系统 shell
    --os-pwn            调出 OOB shell，Meterpreter 或 VNC
    --os-smbrelay       一键调出 OOB shell，Meterpreter 或 VNC
    --os-bof            利用存储过程的缓冲区溢出
    --priv-esc          数据库进程用户提权
    --msf-path=MSFPATH  Metasploit 框架的本地安装路径
    --tmp-path=TMPPATH  远程临时文件目录的绝对路径

  访问 Windows 注册表：
    以下选项用于访问后端数据库管理系统的 Windows 注册表

    --reg-read          读取一个 Windows 注册表键值
    --reg-add           写入一个 Windows 注册表键值数据
    --reg-del           删除一个 Windows 注册表键值
    --reg-key=REGKEY    指定 Windows 注册表键
    --reg-value=REGVAL  指定 Windows 注册表键值
    --reg-data=REGDATA  指定 Windows 注册表键值数据
    --reg-type=REGTYPE  指定 Windows 注册表键值类型

  通用选项：
    以下选项用于设置通用的工作参数

    -s SESSIONFILE      从文件（.sqlite）中加载会话信息
    -t TRAFFICFILE      记录所有 HTTP 流量并保存到文本文件
    --batch             从不询问用户输入，使用默认配置
    --binary-fields=..  具有二进制值的结果字段（例："digest"）
    --charset=CHARSET   获取数据时强制使用的字符编码
    --crawl=CRAWLDEPTH  从目标 URL 开始爬取网站
    --crawl-exclude=..  用正则表达式排除爬取的页面（例："logout"）
    --csv-del=CSVDEL    输出到 CVS 文件时使用的分隔符（默认为“,”）
    --dump-format=DU..  转储数据的格式（CSV（默认），HTML 或 SQLITE）
    --eta               为每个输出显示预计的到达时间
    --flush-session     为当前目标刷新会话文件
    --forms             解析并测试目标 URL 的表单
    --fresh-queries     忽略存储在会话文件中的查询结果
    --hex               获取数据时使用 DBMS 的 hex 函数
    --output-dir=OUT..  自定义输出目录路径
    --parse-errors      从响应中解析并显示 DBMS 错误信息
    --save=SAVECONFIG   将选项设置保存到一个 INI 配置文件
    --scope=SCOPE       用正则表达式从提供的代理日志中过滤目标
    --test-filter=TE..  根据 payloads 和/或标题（例：ROW）选择测试
    --test-skip=TEST..  根据 payloads 和/或标题（例：BENCHMARK）跳过测试
    --update            更新 sqlmap

  其他选项：
    -z MNEMONICS        使用短助记符（例：“flu,bat,ban,tec=EU”）
    --alert=ALERT       在找到 SQL 注入时运行 OS 命令
    --answers=ANSWERS   设置问题答案（例：“quit=N,follow=N”）
    --beep              为问题和/或在找到 SQL 注入时发出提示音
    --cleanup           Clean up the DBMS from sqlmap specific UDF and tables
    --dependencies      检查 sqlmap 缺少什么（非核心）依赖
    --disable-coloring  关闭彩色控制台输出
    --gpage=GOOGLEPAGE  指定页码使用 Google dork 结果
    --identify-waf      针对 WAF/IPS/IDS 保护进行彻底的测试
    --skip-waf          跳过启发式检测 WAF/IPS/IDS 保护
    --mobile            使用 HTTP User-Agent 模仿智能手机
    --offline           在离线模式下工作（仅使用会话数据）
    --page-rank         显示 Google dork 结果的网页排名
    --purge-output      安全地删除输出目录的所有内容
    --smart             只有在正启发时才进行彻底的测试
    --sqlmap-shell      调出交互式 sqlmap shell
    --wizard            适合初级用户的向导界面
```
---
# 原文

# Usage

```
Usage: python sqlmap.py [options]

Options:
  -h, --help            Show basic help message and exit
  -hh                   Show advanced help message and exit
  --version             Show program's version number and exit
  -v VERBOSE            Verbosity level: 0-6 (default 1)

  Target:
    At least one of these options has to be provided to define the
    target(s)

    -d DIRECT           Connection string for direct database connection
    -u URL, --url=URL   Target URL (e.g. "http://www.site.com/vuln.php?id=1")
    -l LOGFILE          Parse target(s) from Burp or WebScarab proxy log file
    -x SITEMAPURL       Parse target(s) from remote sitemap(.xml) file
    -m BULKFILE         Scan multiple targets given in a textual file
    -r REQUESTFILE      Load HTTP request from a file
    -g GOOGLEDORK       Process Google dork results as target URLs
    -c CONFIGFILE       Load options from a configuration INI file

  Request:
    These options can be used to specify how to connect to the target URL

    --method=METHOD     Force usage of given HTTP method (e.g. PUT)
    --data=DATA         Data string to be sent through POST
    --param-del=PARA..  Character used for splitting parameter values
    --cookie=COOKIE     HTTP Cookie header value
    --cookie-del=COO..  Character used for splitting cookie values
    --load-cookies=L..  File containing cookies in Netscape/wget format
    --drop-set-cookie   Ignore Set-Cookie header from response
    --user-agent=AGENT  HTTP User-Agent header value
    --random-agent      Use randomly selected HTTP User-Agent header value
    --host=HOST         HTTP Host header value
    --referer=REFERER   HTTP Referer header value
    -H HEADER, --hea..  Extra header (e.g. "X-Forwarded-For: 127.0.0.1")
    --headers=HEADERS   Extra headers (e.g. "Accept-Language: fr\nETag: 123")
    --auth-type=AUTH..  HTTP authentication type (Basic, Digest, NTLM or PKI)
    --auth-cred=AUTH..  HTTP authentication credentials (name:password)
    --auth-file=AUTH..  HTTP authentication PEM cert/private key file
    --ignore-401        Ignore HTTP Error 401 (Unauthorized)
    --proxy=PROXY       Use a proxy to connect to the target URL
    --proxy-cred=PRO..  Proxy authentication credentials (name:password)
    --proxy-file=PRO..  Load proxy list from a file
    --ignore-proxy      Ignore system default proxy settings
    --tor               Use Tor anonymity network
    --tor-port=TORPORT  Set Tor proxy port other than default
    --tor-type=TORTYPE  Set Tor proxy type (HTTP (default), SOCKS4 or SOCKS5)
    --check-tor         Check to see if Tor is used properly
    --delay=DELAY       Delay in seconds between each HTTP request
    --timeout=TIMEOUT   Seconds to wait before timeout connection (default 30)
    --retries=RETRIES   Retries when the connection timeouts (default 3)
    --randomize=RPARAM  Randomly change value for given parameter(s)
    --safe-url=SAFEURL  URL address to visit frequently during testing
    --safe-post=SAFE..  POST data to send to a safe URL
    --safe-req=SAFER..  Load safe HTTP request from a file
    --safe-freq=SAFE..  Test requests between two visits to a given safe URL
    --skip-urlencode    Skip URL encoding of payload data
    --csrf-token=CSR..  Parameter used to hold anti-CSRF token
    --csrf-url=CSRFURL  URL address to visit to extract anti-CSRF token
    --force-ssl         Force usage of SSL/HTTPS
    --hpp               Use HTTP parameter pollution method
    --eval=EVALCODE     Evaluate provided Python code before the request (e.g.
                        "import hashlib;id2=hashlib.md5(id).hexdigest()")

  Optimization:
    These options can be used to optimize the performance of sqlmap

    -o                  Turn on all optimization switches
    --predict-output    Predict common queries output
    --keep-alive        Use persistent HTTP(s) connections
    --null-connection   Retrieve page length without actual HTTP response body
    --threads=THREADS   Max number of concurrent HTTP(s) requests (default 1)

  Injection:
    These options can be used to specify which parameters to test for,
    provide custom injection payloads and optional tampering scripts

    -p TESTPARAMETER    Testable parameter(s)
    --skip=SKIP         Skip testing for given parameter(s)
    --skip-static       Skip testing parameters that not appear to be dynamic
    --param-exclude=..  Regexp to exclude parameters from testing (e.g. "ses")
    --dbms=DBMS         Force back-end DBMS to this value
    --dbms-cred=DBMS..  DBMS authentication credentials (user:password)
    --os=OS             Force back-end DBMS operating system to this value
    --invalid-bignum    Use big numbers for invalidating values
    --invalid-logical   Use logical operations for invalidating values
    --invalid-string    Use random strings for invalidating values
    --no-cast           Turn off payload casting mechanism
    --no-escape         Turn off string escaping mechanism
    --prefix=PREFIX     Injection payload prefix string
    --suffix=SUFFIX     Injection payload suffix string
    --tamper=TAMPER     Use given script(s) for tampering injection data

  Detection:
    These options can be used to customize the detection phase

    --level=LEVEL       Level of tests to perform (1-5, default 1)
    --risk=RISK         Risk of tests to perform (1-3, default 1)
    --string=STRING     String to match when query is evaluated to True
    --not-string=NOT..  String to match when query is evaluated to False
    --regexp=REGEXP     Regexp to match when query is evaluated to True
    --code=CODE         HTTP code to match when query is evaluated to True
    --text-only         Compare pages based only on the textual content
    --titles            Compare pages based only on their titles

  Techniques:
    These options can be used to tweak testing of specific SQL injection
    techniques

    --technique=TECH    SQL injection techniques to use (default "BEUSTQ")
    --time-sec=TIMESEC  Seconds to delay the DBMS response (default 5)
    --union-cols=UCOLS  Range of columns to test for UNION query SQL injection
    --union-char=UCHAR  Character to use for bruteforcing number of columns
    --union-from=UFROM  Table to use in FROM part of UNION query SQL injection
    --dns-domain=DNS..  Domain name used for DNS exfiltration attack
    --second-order=S..  Resulting page URL searched for second-order response

  Fingerprint:
    -f, --fingerprint   Perform an extensive DBMS version fingerprint

  Enumeration:
    These options can be used to enumerate the back-end database
    management system information, structure and data contained in the
    tables. Moreover you can run your own SQL statements

    -a, --all           Retrieve everything
    -b, --banner        Retrieve DBMS banner
    --current-user      Retrieve DBMS current user
    --current-db        Retrieve DBMS current database
    --hostname          Retrieve DBMS server hostname
    --is-dba            Detect if the DBMS current user is DBA
    --users             Enumerate DBMS users
    --passwords         Enumerate DBMS users password hashes
    --privileges        Enumerate DBMS users privileges
    --roles             Enumerate DBMS users roles
    --dbs               Enumerate DBMS databases
    --tables            Enumerate DBMS database tables
    --columns           Enumerate DBMS database table columns
    --schema            Enumerate DBMS schema
    --count             Retrieve number of entries for table(s)
    --dump              Dump DBMS database table entries
    --dump-all          Dump all DBMS databases tables entries
    --search            Search column(s), table(s) and/or database name(s)
    --comments          Retrieve DBMS comments
    -D DB               DBMS database to enumerate
    -T TBL              DBMS database table(s) to enumerate
    -C COL              DBMS database table column(s) to enumerate
    -X EXCLUDECOL       DBMS database table column(s) to not enumerate
    -U USER             DBMS user to enumerate
    --exclude-sysdbs    Exclude DBMS system databases when enumerating tables
    --pivot-column=P..  Pivot column name
    --where=DUMPWHERE   Use WHERE condition while table dumping
    --start=LIMITSTART  First query output entry to retrieve
    --stop=LIMITSTOP    Last query output entry to retrieve
    --first=FIRSTCHAR   First query output word character to retrieve
    --last=LASTCHAR     Last query output word character to retrieve
    --sql-query=QUERY   SQL statement to be executed
    --sql-shell         Prompt for an interactive SQL shell
    --sql-file=SQLFILE  Execute SQL statements from given file(s)

  Brute force:
    These options can be used to run brute force checks

    --common-tables     Check existence of common tables
    --common-columns    Check existence of common columns

  User-defined function injection:
    These options can be used to create custom user-defined functions

    --udf-inject        Inject custom user-defined functions
    --shared-lib=SHLIB  Local path of the shared library

  File system access:
    These options can be used to access the back-end database management
    system underlying file system

    --file-read=RFILE   Read a file from the back-end DBMS file system
    --file-write=WFILE  Write a local file on the back-end DBMS file system
    --file-dest=DFILE   Back-end DBMS absolute filepath to write to

  Operating system access:
    These options can be used to access the back-end database management
    system underlying operating system

    --os-cmd=OSCMD      Execute an operating system command
    --os-shell          Prompt for an interactive operating system shell
    --os-pwn            Prompt for an OOB shell, Meterpreter or VNC
    --os-smbrelay       One click prompt for an OOB shell, Meterpreter or VNC
    --os-bof            Stored procedure buffer overflow exploitation
    --priv-esc          Database process user privilege escalation
    --msf-path=MSFPATH  Local path where Metasploit Framework is installed
    --tmp-path=TMPPATH  Remote absolute path of temporary files directory

  Windows registry access:
    These options can be used to access the back-end database management
    system Windows registry

    --reg-read          Read a Windows registry key value
    --reg-add           Write a Windows registry key value data
    --reg-del           Delete a Windows registry key value
    --reg-key=REGKEY    Windows registry key
    --reg-value=REGVAL  Windows registry key value
    --reg-data=REGDATA  Windows registry key value data
    --reg-type=REGTYPE  Windows registry key value type

  General:
    These options can be used to set some general working parameters

    -s SESSIONFILE      Load session from a stored (.sqlite) file
    -t TRAFFICFILE      Log all HTTP traffic into a textual file
    --batch             Never ask for user input, use the default behaviour
    --binary-fields=..  Result fields having binary values (e.g. "digest")
    --charset=CHARSET   Force character encoding used for data retrieval
    --crawl=CRAWLDEPTH  Crawl the website starting from the target URL
    --crawl-exclude=..  Regexp to exclude pages from crawling (e.g. "logout")
    --csv-del=CSVDEL    Delimiting character used in CSV output (default ",")
    --dump-format=DU..  Format of dumped data (CSV (default), HTML or SQLITE)
    --eta               Display for each output the estimated time of arrival
    --flush-session     Flush session files for current target
    --forms             Parse and test forms on target URL
    --fresh-queries     Ignore query results stored in session file
    --hex               Use DBMS hex function(s) for data retrieval
    --output-dir=OUT..  Custom output directory path
    --parse-errors      Parse and display DBMS error messages from responses
    --save=SAVECONFIG   Save options to a configuration INI file
    --scope=SCOPE       Regexp to filter targets from provided proxy log
    --test-filter=TE..  Select tests by payloads and/or titles (e.g. ROW)
    --test-skip=TEST..  Skip tests by payloads and/or titles (e.g. BENCHMARK)
    --update            Update sqlmap

  Miscellaneous:
    -z MNEMONICS        Use short mnemonics (e.g. "flu,bat,ban,tec=EU")
    --alert=ALERT       Run host OS command(s) when SQL injection is found
    --answers=ANSWERS   Set question answers (e.g. "quit=N,follow=N")
    --beep              Beep on question and/or when SQL injection is found
    --cleanup           Clean up the DBMS from sqlmap specific UDF and tables
    --dependencies      Check for missing (non-core) sqlmap dependencies
    --disable-coloring  Disable console output coloring
    --gpage=GOOGLEPAGE  Use Google dork results from specified page number
    --identify-waf      Make a thorough testing for a WAF/IPS/IDS protection
    --skip-waf          Skip heuristic detection of WAF/IPS/IDS protection
    --mobile            Imitate smartphone through HTTP User-Agent header
    --offline           Work in offline mode (only use session data)
    --page-rank         Display page rank (PR) for Google dork results
    --purge-output      Safely remove all content from output directory
    --smart             Conduct thorough tests only if positive heuristic(s)
    --sqlmap-shell      Prompt for an interactive sqlmap shell
    --wizard            Simple wizard interface for beginner users
```