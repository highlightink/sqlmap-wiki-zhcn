## 请求

以下选项用于指定如何连接目标 URL。

### HTTP 方法

选项：`--method`

sqlmap 能自动检测 HTTP 请求中使用的 HTTP 方法。然而在某些情况下，可能需要强制指定使用 sqlmap 自动化不会使用的 HTTP 方法（例如：`PUT`）。因而该选项是可能被用到的（例如：`--method=PUT`）。

### HTTP 数据

选项：`--data`

HTTP 请求默认使用的方法是 GET，你可以通过在请求中提供对应发送的数据隐式地将 GET 改成 POST。对应参数也会像 GET 参数一样，用于测试是否存在 SQL 注入的可能。

例如：

```
$ python sqlmap.py -u "http://www.target.com/vuln.php" --data="id=1" -f --banne\
r --dbs --users
```

### 参数分隔符

选项：`--param-del`

有些情况下，需要覆盖默认参数分隔符（例如：在 GET 和 POST 数据中的 `&`），以便 sqlmap 能够正确切割并处理每个参数。

例如：

```
$ python sqlmap.py -u "http://www.target.com/vuln.php" --data="query=foobar;id=\
1" --param-del=";" -f --banner --dbs --users
```

### HTTP `Cookie` 请求头

选项和开关：`--cookie`，`--cookie-del`，`--load-cookies` 和 `--drop-set-cookie`

这些选项和开关可用于以下两种情况：

* Web 应用程序需要基于 cookies 的身份验证，并且你知道对应的参数。
* 你想对相关的 HTTP 头部进行检测和 SQL 注入。

不管是哪种情况，你需要使用 sqlmap 发送带有 cookies 的请求，步骤如下：

* 使用你最喜欢的浏览器登录该应用。
* 从浏览器的选项或 HTTP 代理中复制 Cookie。
* 回到 shell 并使用复制的 cookies 作为选项 `--cookie` 的值运行 sqlmap。

注意，HTTP `Cookie` 值通常由字符 `;` 分隔，而**不是使用** `&`。sqlmap 也可以将它们识别为 `parameter=value` 即参数值对，对应的 GET 和 POST 参数也一样。如果分隔字符不是 `;`，则可以使用选项 `--cookie-del` 来指定。

在通信期间的任何时刻，如果 Web 应用程序的响应包含 `Set-Cookie` 响应头，sqlmap 将在所有其他 HTTP 请求中自动使用它的值作为 `Cookie` 的值。sqlmap 也将自动测试这些值是否存在 SQL 注入漏洞。这个特性可以通过提供开关 `--drop-set-cookie` 来关闭——sqlmap 则会忽略任何 `Set-Cookie` 响应头。

反之亦然，如果你提供一个带有选项 `--cookie` 的 HTTP `Cookie` 请求头，并且目标 URL 在任何时候都发送一个 HTTP `Set-Cookie` 响应头，sqlmap 会询问你使用哪一组 cookies 来用于接下来的 HTTP 请求。

还有一个选项 `--load-cookies`，可以从包含 Netscape/wget 格式 cookies 的特殊文件中读取 cookies。

注意，如果 `--level` 设置为 **2** 或更高，则 sqlmap 会对 HTTP `Cookie` 请求头进行 SQL 注入测试。详情请看下文。

### HTTP `User-Agent` 请求头

选项和开关：`--user-agent` 和 `--random-agent`

默认情况下，sqlmap 使用以下 `User-Agent` 请求头值执行 HTTP 请求：

```
sqlmap/1.0-dev-xxxxxxx (http://sqlmap.org)
```

不过，可以通过提供自定义 User-Agent 作为选项的参数，即选项 `--user-agent` 来伪造它。

此外，如果通过提供开关 `--random-agent`，sqlmap 将从 `./txt/user-agents.txt` 文本文件中随机选择一个 `User-Agent`，并将其用于该会话中的所有 HTTP 请求。

一些站点会对 HTTP `User-Agent` 请求头值进行服务端检查，如果没有提供有效的 `User-Agent`，它的值不是常规值或被 Web 应用程序防火墙或类似防御系统列入黑名单，则服务端会拒绝 HTTP 响应。在这种情况下，sqlmap 将显示如下信息：

```
[hh:mm:20] [ERROR] the target URL responded with an unknown HTTP status code, try to 
force the HTTP User-Agent header with option --user-agent or --random-agent
译：
[hh:mm:20] [错误] 目标网址回复了未知的 HTTP 状态码，请尝试使用选项 --user-agent 或 
--random-agent 强制指定 HTTP User-Agent 请求头
```

注意，如果 `--level` 设置为 **3** 或以上，sqlmap 会对 HTTP `User-Agent` 请求头进行 SQL 注入测试。详情请看下文。

### HTTP `Host` 请求头

选项：`--host`

你可以手动设置 HTTP `Host` 请求头值。默认情况下，HTTP `Host` 请求头从提供的目标 URL 中解析。

注意，如果 `--level` 设置为 **5** 或以上，sqlmap 会对 HTTP `User-Agent` 请求头进行 SQL 注入测试。详情请看下文。

### HTTP `Referer` 请求头

选项：`--referer`

支持伪造 HTTP `Referer` 请求头值。如果**没有**进行显式设置，默认情况下不会在 HTTP 请求中发送 HTTP `Referer` 请求头。

注意，如果 `--level` 设置为 **3** 或更高，sqlmap 会对 HTTP `Referer` 请求头进行 SQL 注入测试。详情请看下文。

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

这些选项用于指定后端 Web 服务器实现的 HTTP 协议认证和所有向目标程序发起 HTTP 请求的有效凭据。

支持的三种 HTTP 协议认证机制是：

* `Basic`
* `Digest`
* `NTLM`

认证凭据的语法是 `username:password`。

一个符合语法的例子：

```
$ python sqlmap.py -u "http://192.168.136.131/sqlmap/mysql/basic/get_int.php?id\
=1" --auth-type Basic --auth-cred "testuser:testpass"
```

### HTTP 协议私钥认证

选项：`--auth-file`

当 Web 服务器需要正确的客户端证书和私钥进行身份验证时，应使用此选项。提供的值应为包含证书和私钥的 PEM 格式文件 `key_file`。

生成适用于 `--auth-file` 选项的 `key_file.txt` 文件示例：

```
$ openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout auth_file.key -out auth_file.pem &&\
cat auth_file.key auth_file.pem > auth_file.txt && cat auth_file.txt
Generating a 2048 bit RSA private key
.........+++
...........+++
writing new private key to 'auth_file.key'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:
State or Province Name (full name) [Some-State]:
Locality Name (eg, city) []:
Organization Name (eg, company) [Internet Widgits Pty Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (e.g. server FQDN or YOUR name) []:
Email Address []:
-----BEGIN PRIVATE KEY-----
MIIEvwIBADANBgkqhkiG9w0BAQEFAASCBKkwggSlAgEAAoIBAQCWM28J1ua2DINf
VLU28oeJwQidL9vTRoGJR5pfBU6Mhu33Cv6RuVEJAfMWEKYDSbqbrEyy1zUiNTcG
mEd026Peq0SPRvsKsVb6K+EHVF3r+6ExXHEctPRbh2GIzi2kCQMkdHDg+DhmieQ9
9Haxk9IREJZTo2vC1ohvM5M/yubw4iwgMlDaW+4s82OgOcCLjewbPLFZU5gMV+8W
XLKUttUYwV79duPbEvG9S1soNFHhu/MOcNiKJpH2zSegd9Dk5/OJRGX5xEiv7AyL
4shQLpAqn5kuZcm2K+ib/4x/Rw2yT1Slh2tQIi8RcwlYyycOrSqvhW7vvdqkblbY
mQQyR2ChAgMBAAECggEBAIqvMveC1cOCCksbi7cQeNVYxvtcFT0e/LwkwQS7gat/
anmQTT2APrJyemEFPkQK76KNlMQMsaLEP+p28IOVydjvin5Aq8tTs1uK6Fw8Kfya
elt5X3eCHZ3lgskuljW/nIcsfI08o9cJuxT5hB6yvmPDTQos+nMMYy1KEcv1LQd8
Y+QAmVQqMF5Nyf8Q6op6hWZIIJY5NDbRE0zYzhGcHWg2798Dx1sO0HT6TD8cNP8H
AVp/V21tzpmFpe0A7NajgYEjkij6fg+6mG0j0WZdWymYXDeiTdDpwzs/SPRevBLn
Okp/6vqtdekMeYL591MNBl8GRZpJW9gNLRX7vQ6YYAECgYEAxGV9e85GpLUd/uUb
1MvGajd+HtN/uoWH1ySG34vi3q/dDKBehry2yoDUosxXf9vbH0IrvaXnO8yXGflS
wb2TELLezGWbw6kPaw2XIgL4elO5TPh2rNJwz1wOhv3FT2XSGJbXx/CED3mL7MGs
qwRU/bRrNV7RmzV2veThlLCLjZECgYEAw8jm7vOzQQnqEjs0wlfJmzOyFqilYvEP
8v7HxDv1M7e7M0TqLECET9VlQE5spGuzEWN7/iMtE8xxnz2n/vGnGAV8qv1LJYrA
TWQMTIC6V9/jKM8wNOfT7Eh1rJ1cty87yokXpy/cdmkv7yxb1b2zuBk8/1nlYqA0
5uqb345eWhECgYEAmoXv0TVfR8BpNWA2IZujJXc7+C0YVj0xwAixRbneaq+cEI8t
UH2ypGnw45Y7UhI9ub5qg/DAmsBCMuGER4NM7tqNiex4Pd4Kj4RF4TDNKBIvvWvQ
k/GPaNdZZsTMNcg7IbWtWVbX0QUlHsbTgEsMRAFsSLWt3ZyXLJmlE0REyMECgYEA
oCqEscrwRC7GLK/+01ZZ+fvqnxrMYgrvj0zbRDAAwpR2MtUX9ae6Fk1vDZKa0k/B
KGKIlzlTsTS5ZxpbivdKSR6EBKY+ibHe6/EDFrrgtu7TuRj2SPG2rz//9Hyv0rRz
Z5eLoBxJcR1QN4vEfTE6C0uqWQPD4lFJtfcMGXEwwuECgYAK+4gwPBlrKClrRtDc
7Fnq8RLYeZRbM5WEmTHfRnlYylniMsj2K20H8ln8pdOqCE4iJn0SezIQIaAtcwMP
WQt15kgJgLwM/uBtqDeWRpTEotVMFXQbZImobjpXUhTqu0NWBwbypM/zarfRWPJ4
fJkrlA16caVj3qGaX1lkm06OAA==
-----END PRIVATE KEY-----
-----BEGIN CERTIFICATE-----
MIIDXTCCAkWgAwIBAgIJALTHPlkIs/+KMA0GCSqGSIb3DQEBCwUAMEUxCzAJBgNV
BAYTAkFVMRMwEQYDVQQIDApTb21lLVN0YXRlMSEwHwYDVQQKDBhJbnRlcm5ldCBX
aWRnaXRzIFB0eSBMdGQwHhcNMTgwODIyMDc0NTQxWhcNMTkwODIyMDc0NTQxWjBF
MQswCQYDVQQGEwJBVTETMBEGA1UECAwKU29tZS1TdGF0ZTEhMB8GA1UECgwYSW50
ZXJuZXQgV2lkZ2l0cyBQdHkgTHRkMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIB
CgKCAQEAljNvCdbmtgyDX1S1NvKHicEInS/b00aBiUeaXwVOjIbt9wr+kblRCQHz
FhCmA0m6m6xMstc1IjU3BphHdNuj3qtEj0b7CrFW+ivhB1Rd6/uhMVxxHLT0W4dh
iM4tpAkDJHRw4Pg4ZonkPfR2sZPSERCWU6NrwtaIbzOTP8rm8OIsIDJQ2lvuLPNj
oDnAi43sGzyxWVOYDFfvFlyylLbVGMFe/Xbj2xLxvUtbKDRR4bvzDnDYiiaR9s0n
oHfQ5OfziURl+cRIr+wMi+LIUC6QKp+ZLmXJtivom/+Mf0cNsk9UpYdrUCIvEXMJ
WMsnDq0qr4Vu773apG5W2JkEMkdgoQIDAQABo1AwTjAdBgNVHQ4EFgQUVvHI/2qF
kmRCEWlWB+ZvJzWTnUkwHwYDVR0jBBgwFoAUVvHI/2qFkmRCEWlWB+ZvJzWTnUkw
DAYDVR0TBAUwAwEB/zANBgkqhkiG9w0BAQsFAAOCAQEAg5tmkM75/NEEymu0ublj
c2R1/ZxwbKMjg98KxLqGFJbPVRG0qgIy+uc+Gvh6FEgPF22i4L9DROfuDQW3YSJ6
x3JnJxLsU+jjXxtN7hNwoQziQkicKr0y47TjqOKLlBlKTbdnr74nJXSYQhi4qEFE
qgrUG7ScitgLvcf2sDVf9L2SUsH5iRK+HlgYEtSKhUl5SkLapcUUF+GmectUOkm7
m7Z8gelenVUerLojnQL2avKD07hWTTGkgX2PV8hdun0WIvBLWAcJN+6T9sdakJZZ
qJjFQBXjcxwgVe0vB0vJmqa5lj9OymQnBMjp+3zpUtDJNH2M1qySbU6tGEX1wsW/
VA==
-----END CERTIFICATE-----
```

### 忽略 HTTP 401（未授权）错误

开关 `--ignore-401`

如果你测试的目标站点偶尔会返回 HTTP 401（未授权）错误，而你想忽略它，不提供正确的凭据并继续测试，你可以使用开关`--ignore-401` 来关闭对应的出错提醒。

### HTTP(S) 代理

选项和开关：`--proxy`，`--proxy-cred`，`--proxy-file` 和 `--ignore-proxy`

可以使用选项 `--proxy` 并提供 HTTP(S) 代理地址使 HTTP(S) 请求经过该代理到达目标 URL。设置 HTTP(S) 代理的语法是 `http://url:port`。

如果 HTTP(S) 代理需要身份验证，则可以对选项 `--proxy-cred` 使用 `username:password` 格式添加对应的凭证。

如果要使用（不稳定的）代理列表，在可能出现连接问题（例如：阻止侵入性 IP 地址）出现时跳过并使用下一个代理，可以使用选项 `--proxy-file` 并指定包含批量代理的文件。

当你想要使用 sqlmap 对本地局域网目标进行测试时应该使用开关 `--ignore-proxy` 来绕过系统级的 HTTP(S) 代理服务。

### Tor 匿名网络

开关和选项：`--tor`，`--tor-port`，`--tor-type` 和 `--check-tor`

假如因为相关原因需要保持匿名，可以根据 [Tor 安装指南](https://www.torproject.org/docs/installguide.html.en)配置一个 [Tor 客户端](http://www.torproject.org/)和 [Privoxy](http://www.privoxy.org)（或类似的）进行代理，而不是使用单个预定义的 HTTP(S) 代理服务器。接着就可以使用开关 `--tor` 来让 sqlmap 尝试自动设置 Tor 代理连接。

如果你想手动设置 Tor 代理的类型和端口，可以使用选项 `--tor-type` 和 `--tor-port`（例如：`--tor-type=SOCKS5 --tor-port 9050`）。

强烈建议偶尔使用 `--check-tor` 来确保一切设置正确。有些情况下 Tor 包（例如：Vidalia（译者注：Vidalia 是 Tor 的图形界面管理工具，官方已经移除对它的支持））配置错误（或重置了以前的配置）会使你以为已经成功匿名。使用这个开关，sqlmap 将在对任何目标发起请求之前发送一个请求到[你正在使用 Tor？](https://check.torproject.org/)这个官方页面检查一切配置是否正常。如果检查失败，sqlmap 将警告你并直接退出。

### 每个 HTTP 请求之间的延迟

选项：`--delay`

可以指定每个 HTTP(S) 请求之间等待的秒数。有效值是一个浮点数，例如 `0.5` 表示半秒。默认情况下，没有设置延迟。

### 超时连接等待秒数

选项：`--timeout`

可以指定 HTTP(S) 请求超时的等待秒数。有效值是一个浮点数，例如 10.5 表示十秒半。默认设置是 **30 秒**。

### HTTP 连接超时最大重试次数

选项：`--retries`

可以指定 HTTP(S) 连接超时的最大重试次数。默认情况下，它最多重试**三次**。

### 随机更改给定参数的值

选项：`--randomize`

可以指定在每个请求期间需要随机更改其值的参数名称。长度和类型由提供的原始值决定。

### 使用正则表达式从指定的代理日志中提取目标

选项：`--scope`

你可以指定有效的 Python 正则表达式用于提取出所需的目标，而不是使用由选项 `-l` 从日志中解析出的所有主机目标。

有效语法示例：

```
$ python sqlmap.py -l burp.log --scope="(www)?\.target\.(com|net|org)"
```

### 避免因太多失败请求引发会话销毁

选项：`--safe-url`，`--safe-post`，`--safe-req` 和 `--safe-freq`

有时，在执行了一定数量的失败请求后会被 Web 应用或检测技术销毁相关会话。这可能发生在 sqlmap 的检测阶段，或者在它利用任何 SQL 盲注时。原因是 SQL payloads 不一定会返回输出，因此这可能会向应用会话管理或检测技术暴露出特征。

要绕过目标站点设置的这种限制，你可以提供任何（或组合）以下选项：

* `--safe-url`：测试期间可以安全频繁访问的 URL 地址。
* `--safe-post`：使用 HTTP POST 发送数据到一个安全的 URL 地址。
* `--safe-req`：从文件中加载并使用安全的 HTTP 请求。
* `--safe-freq`：交替执行指定的安全地址访问和目标测试请求。

这样，sqlmap 将访问每个定义好数量请求的某个_安全_ URL，而不对其执行任何类型的注入。

### 关闭对参数值的 URL 编码

开关：`--skip-urlencode`

根据参数的位置（例如：GET），其值可能会被默认进行 URL 编码。在某些情况下，后端 Web 服务器不遵循 RFC 标准，并要求以原始非编码形式发送参数值。在这种情况下可以使用 `--skip-urlencode`。

### 绕过反 CSRF 防护

选项：`--csrf-token` 和 `--csrf-url`

许多站点有使用 token 的反 CSRF 防护，在每个页面的响应随机设置隐藏字段值。sqlmap 将自动尝试识别并绕过这种防护，同时支持 `--csrf-token` 和 `--csrf-url` 等选项用来做进一步调整。选项 `--csrf-token` 用于设置包含随机 token 的隐藏字段的名称。这在网站对这些字段使用非标准名称的情况下是非常有用的。选项 `--csrf-url` 用于从任意有效的 URL 地址获取 token 值。这在目标网址在初始地不包含必需的 token 值，而需要从其他地方提取时是非常有用的。

### 强制使用 SSL/HTTPS

开关：`--force-ssl`

如果想要对目标强制使用 SSL/HTTPS 请求，可以使用此开关。在使用选项 `--crawl` 收集 URLs 或者使用选项 `-l` 提供 Burp 日志时，该开关是很有用的。

### 在每个请求期间运行自定义的 Python 代码

选项：`--eval`

在可能因为一些已知的依赖而想要更改（或添加新的）参数值的情况下，可以使用选项 `--eval` 为 sqlmap 提供自定义的 python 代码，代码将在每个请求之前运行。

例如：

```
$ python sqlmap.py -u "http://www.target.com/vuln.php?id=1&hash=c4ca4238a0b9238\
20dcc509a6f75849b" --eval="import hashlib;hash=hashlib.md5(id).hexdigest()"
```

每个像这样的请求会使用当前 GET 请求中的 `id` 参数值重新计算出对应的 MD5 哈希值，从而替换掉原来的 `hash` 参数值。