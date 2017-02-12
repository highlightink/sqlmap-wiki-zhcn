## 请求

以下选项用于指定连接目标 URL 的方式。

### HTTP 方法

选项：`--method`

sqlmap 能自动检测 HTTP 请求中使用的 HTTP 方法。然而在某些情况下，需要强制使用 sqlmap 自动化不使用的特定 HTTP 方法（例：`PUT`）。该选项是可能被用到的（例：`--method=PUT`）。

### HTTP 数据

选项：`--data`

HTTP 请求默认使用的方法是 GET，你可以通过在请求中提供要发送的数据隐式地将 GET 改成 POST。这些参数也会像 GET 参数一样被测试是否存在 SQL 注入。

例如：

```
$ python sqlmap.py -u "http://www.target.com/vuln.php" --data="id=1" -f --banne\
r --dbs --users
```

### 参数分隔符

选项：`--param-del`

有些情况下，需要覆盖默认参数分隔符（例：`&` 在 GET 和 POST 数据中），以使 sqlmap 能够正确地分别处理每个参数。

例如：

```
$ python sqlmap.py -u "http://www.target.com/vuln.php" --data="query=foobar;id=\
1" --param-del=";" -f --banner --dbs --users
```

### HTTP `Cookie` 请求头

选项和开关：`--cookie`，`--cookie-del`，`--load-cookies` 和 `--drop-set-cookie`

这些选项和开关可用于以下两种情况：

* Web 应用程序需要基于 cookies 的身份验证，而你拥有该数据。
* 你想对 HTTP 头部检测和利用 SQL 注入。

不管是哪种情况，你需要使用 sqlmap 发送带有 cookies 的请求，步骤如下：

* 使用你最喜欢的浏览器登录该应用。
* 从浏览器的选项或 HTTP 代理中复制 Cookie。
* 回到 shell 并使用复制的 cookies 作为选项 `--cookie` 的值运行 sqlmap。

注意，HTTP `Cookie` 值通常由字符 `;` 分隔，而**不是** `&`。sqlmap 也可以将它们识别为 `parameter=value` 即参数值对，GET 和 POST 参数也一样。如果分隔字符不是 `;`，则可以使用选项 `--cookie-del` 来指定。

如果在通信期间的任何时刻，Web 应用程序的响应包含 `Set-Cookie` 响应头，sqlmap 将在所有其他 HTTP 请求中自动使用它的值作为 `Cookie` 的值。sqlmap 也将自动测试这些值是否存在 SQL 注入。这可以通过提供开关 `--drop-set-cookie` 来避免—— sqlmap 将忽略任何 `Set-Cookie` 响应头。

反之亦然，如果你提供一个带有选项 `--cookie` 的 HTTP `Cookie` 请求头，同时目标 URL 在任何时候都会发送一个 HTTP `Set-Cookie` 响应头，sqlmap 会询问你使用哪一组 cookies 来用于接下来的 HTTP 请求。

还有一个选项 `--load-cookies`，可以使用一个包含 Netscape/wget 格式 cookies 的特殊文件。

注意，如果 `--level` 设置为 **2** 或更高，HTTP `Cookie` 请求头也会针对 SQL 注入被进行测试。详情请看下文。

### HTTP `User-Agent` 请求头

选项和开关：`--user-agent` 和 `--random-agent`

默认情况下，sqlmap 使用以下 `User-Agent` 请求头值执行 HTTP 请求：

```
sqlmap/1.0-dev-xxxxxxx (http://sqlmap.org)
```

不过，可以通过提供自定义 User-Agent 作为选项的参数，即选项 `--user-agent` 来伪造它。

此外，通过提供开关 `--random-agent`，sqlmap 将从 `./txt/user-agents.txt` 文本文件中随机选择一个 `User-Agent`，并将其用于会话中的所有 HTTP 请求。

一些站点会对 HTTP `User-Agent` 请求头值进行服务器端检查，如果没有提供有效的 `User-Agent`，它的值不被预期或被 Web 应用程序防火墙或类似防御系统列入黑名单，则会拒绝 HTTP 响应。在这种情况下，sqlmap 将显示如下消息：

```
[hh:mm:20] [ERROR] the target URL responded with an unknown HTTP status code, try to 
force the HTTP User-Agent header with option --user-agent or --random-agent
译：
[hh:mm:20] [错误] 目标网址回复了未知的 HTTP 状态码，请尝试使用选项 --user-agent 或 
--random-agent 强制指定 HTTP User-Agent 请求头
```

注意，如果 `--level` 设置为 **3** 或以上，HTTP `User-Agent` 请求头也会针对 SQL 注入被进行测试。详情请看下文。

### HTTP `Host` 请求头

选项：`--host`

你可以手动设置 HTTP `Host` 请求头值。默认情况下，HTTP `Host` 请求头从提供的目标 URL 中解析。

注意，如果 `--level` 设置为 **5**，HTTP `Host` 请求头也会针对 SQL 注入被进行测试。详情请看下文。

### HTTP `Referer` 请求头

选项：`--referer`

可以伪造 HTTP `Referer` 请求头值。如果**没有**显式设置，默认情况下不会在 HTTP 请求中发送 HTTP `Referer` 请求头。

注意，如果 `--level` 设置为 **3** 或更高，HTTP `Referer` 请求头也会针对 SQL 注入被进行测试。详情请看下文。

### 额外的 HTTP 请求头

选项：`--headers`

可以通过设置选项 `--headers` 来提供额外的 HTTP 请求头。每个请求头必须用换行符分隔，更好的方式是从 INI 配置文件读取。你可以看看范本 `sqlmap.conf` 文件中的例子。

针对 MySQL 目标的示例：

```
$ python sqlmap.py -u "http://192.168.21.128/sqlmap/mysql/get_int.php?id=1" -z \
"ign,flu,bat,tec=E" --headers="Host:www.target.com\nUser-agent:Firefox 1.0" -v 5
[...]
[xx:xx:44] [TRAFFIC OUT] HTTP request [#5]:
GET /sqlmap/mysql/get_int.php?id=1%20AND%20%28SELECT%209351%20FROM%28SELECT%20C\
OUNT%28%2A%29%2CCONCAT%280x3a6161733a%2C%28SELECT%20%28CASE%20WHEN%20%285473%20\
%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%\
20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%2\
0%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20\
%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%\
20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%2\
0%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20\
%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%\
20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%2\
0%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20\
%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%3D%20%20%20%20%20%20%20%\
20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%2\
0%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20\
%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%\
20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%2\
0%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20\
%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%\
20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%2\
0%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20\
%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%\
20%20%20%20%20%20%20%20%20%20%20%205473%29%20THEN%201%20ELSE%200%20END%29%29%2C\
0x3a6c666d3a%2CFLOOR%28RAND%280%29%2A2%29%29x%20FROM%20INFORMATION_SCHEMA.CHARA\
CTER_SETS%20GROUP%20BY%20x%29a%
29 HTTP/1.1
Host: www.target.com
Accept-encoding: gzip,deflate
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
User-agent: Firefox 1.0
Connection: close
[...]
```

### HTTP 协议认证

选项：`--auth-type` 和 `--auth-cred`

These options can be used to specify which HTTP protocol authentication back-end web server implements and the valid credentials to be used to perform all HTTP requests to the target application.

The three supported HTTP protocol authentication mechanisms are:

* `Basic`
* `Digest`
* `NTLM`

While the credentials' syntax is `username:password`.

Example of valid syntax:

```
$ python sqlmap.py -u "http://192.168.136.131/sqlmap/mysql/basic/get_int.php?id\
=1" --auth-type Basic --auth-cred "testuser:testpass"
```

### HTTP protocol private key authentication

Option: `--auth-file`

This option should be used in cases when the web server requires proper client-side certificate and a private key for authentication. Supplied value should be a PEM formatted `key_file` that contains your certificate and a private key.


### Ignore HTTP error 401 (Unauthorized)

Switch `--ignore-401`

In case that you want to test the site that occasionally returns HTTP error 401 (Unauthorized), while you want to ignore it and continue tests without providing proper credentials, you can use switch `--ignore-401`

### HTTP(S) proxy

Options and switch: `--proxy`, `--proxy-cred`, `--proxy-file` and `--ignore-proxy`

It is possible to provide an HTTP(S) proxy address to pass by the HTTP(S) requests to the target URL with option `--proxy`. The syntax of HTTP(S) proxy value is `http://url:port`.

If the HTTP(S) proxy requires authentication, you can provide the credentials in the format `username:password` to the
option `--proxy-cred`.

In case that you want to use (disposable) proxy list, skipping to the next proxy on any sign of a connection problem (e.g. blocking of invasive IP address), option `--proxy-file` can be used by providing filename of a file containing bulk list of proxies.

Switch `--ignore-proxy` should be used when you want to run sqlmap against a target part of a local area network by ignoring the system-wide set HTTP(S) proxy server setting.

### Tor anonymity network

Switches and options: `--tor`, `--tor-port`, `--tor-type` and `--check-tor`

If, for any reason, you need to stay anonymous, instead of passing by a single predefined HTTP(S) proxy server, you can configure a [Tor client](http://www.torproject.org/) together with [Privoxy](http://www.privoxy.org) (or similar) on your machine as explained in [Tor installation guides](https://www.torproject.org/docs/installguide.html.en). Then you can use a switch `--tor` and sqlmap will try to automatically set Tor proxy connection settings.

In case that you want to manually set the type and port of used Tor proxy, you can do it with options `--tor-type` and `--tor-port` (e.g. `--tor-type=SOCKS5 --tor-port 9050`).

You are strongly advised to use `--check-tor` occasionally to be sure that everything was set up properly. There are cases when Tor bundles (e.g. Vidalia) come misconfigured (or reset previously set configuration) giving you a false sense of anonymity. Using this switch sqlmap will check that everything works as expected by sending a single request to an official [Are you using Tor?](https://check.torproject.org/) page before any target requests. In case that check fails, sqlmap will warn you and abruptly exit.

### Delay between each HTTP request

Option: `--delay`

It is possible to specify a number of seconds to hold between each HTTP(S) request. The valid value is a float, for instance `0.5` means half a second. By default, no delay is set.

### Seconds to wait before timeout connection

Option: `--timeout`

It is possible to specify a number of seconds to wait before considering the HTTP(S) request timed out. The valid value is a float, for instance 10.5 means ten seconds and a half. By default **30 seconds** are set.

### Maximum number of retries when the HTTP connection timeouts

Option: `--retries`

It is possible to specify the maximum number of retries when the HTTP(S) connection timeouts. By default it retries up to **three times**.

### Randomly change value for given parameter(s)

Option: `--randomize`

It is possible to specify parameter names whose values you want to be randomly changed during each request. Length and type are being kept according to provided original values.

### Filtering targets from provided proxy log using regular expression

Option: `--scope`

Rather than using all hosts parsed from provided logs with option `-l`, you can specify valid Python regular expression to be used for filtering desired ones.

Example of valid syntax:

```
$ python sqlmap.py -l burp.log --scope="(www)?\.target\.(com|net|org)"
```

### Avoid your session to be destroyed after too many unsuccessful requests

Options: `--safe-url`, `--safe-post`, `--safe-req` and `--safe-freq`

Sometimes web applications or inspection technology in between destroys the session if a certain number of unsuccessful requests is performed. This might occur during the detection phase of sqlmap or when it exploits any of the blind SQL injection types. Reason why is that the SQL payload does not necessarily returns output and might therefore raise a signal to either the application session management or the inspection technology.

To bypass this limitation set by the target, you can provide any (or combination of) option:

* `--safe-url`: URL address to visit frequently during testing.
* `--safe-post`: HTTP POST data to send to a given safe URL address.
* `--safe-req`: Load and use safe HTTP request from a file.
* `--safe-freq`: Test requests between two visits to a given safe location.

This way, sqlmap will visit every a predefined number of requests a certain _safe_ URL without performing any kind of injection against it.

### Turn off URL encoding of parameter values

Switch: `--skip-urlencode`

Depending on parameter placement (e.g. GET) its value could be URL encoded by default. In some cases, back-end web servers do not follow RFC standards and require values to be send in their raw non-encoded form. Use `--skip-urlencode` in those kind of cases.

### Bypass anti-CSRF protection

Options: `--csrf-token` and `--csrf-url`

Lots of sites incorporate anti-CSRF protection in form of tokens, hidden field values that are randomly set during each page response. sqlmap will automatically try to recognize and bypass that kind of protection, but there are options `--csrf-token` and `--csrf-url` that can be used to furter fine tune it. Option `--csrf-token` can be used to set the name of the hidden value that contains the randomized token. This is useful in cases when web sites use non-standard names for such fields. Option `--csrf-url` can be used for retrieval of the token value from arbitrary URL address. This is useful if the vulnerable target URL doesn't contain the necessary token value in the first place, but it is required to extract it from some other location.

### Force usage of SSL/HTTPS

Switch: `--force-ssl`

In case that user wants to force usage of SSL/HTTPS requests toward the target, he can use this switch. This can be useful in cases when urls are being collected by using option `--crawl` or when Burp log is being provided with option `-l`.

### Evaluate custom python code during each request

Option: `--eval`

In case that user wants to change (or add new) parameter values, most probably because of some known dependency, he can provide to sqlmap a custom python code with option `--eval` that will be evaluated just before each request.

For example:

```
$ python sqlmap.py -u "http://www.target.com/vuln.php?id=1&hash=c4ca4238a0b9238\
20dcc509a6f75849b" --eval="import hashlib;hash=hashlib.md5(id).hexdigest()"
```

Each request of such run will re-evaluate value of GET parameter `hash` to contain a fresh MD5 hash digest for current value of parameter `id`.