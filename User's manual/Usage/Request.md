## 请求

以下选项用于指定连接目标 URL 的方式。

### HTTP 方法

选项：`--method`

sqlmap 能自动检测 HTTP 请求中使用的 HTTP 方法。然而在某些情况下，需要强制使用 sqlmap 自动化不使用的特定 HTTP 方法（例：`PUT`）。该选项是可能被用到的（例：`--method=PUT`）。

### HTTP data

Option: `--data`

By default the HTTP method used to perform HTTP requests is GET, but you can implicitly change it to POST by providing the data to be sent in the POST requests. Such data, being those parameters, are tested for SQL injection as well as any provided GET parameters.

For example:

```
$ python sqlmap.py -u "http://www.target.com/vuln.php" --data="id=1" -f --banne\
r --dbs --users
```

### Parameter splitting character

Option: `--param-del`

There are cases when default parameter delimiter (e.g. `&` in GET and POST data) needs to be overwritten for sqlmap to be able to properly split and process each parameter separately.

For example:

```
$ python sqlmap.py -u "http://www.target.com/vuln.php" --data="query=foobar;id=\
1" --param-del=";" -f --banner --dbs --users
```

### HTTP `Cookie` header

Options and switch: `--cookie`, `--cookie-del`, `--load-cookies` and `--drop-set-cookie`

These options and switches can be used in two situations:

* The web application requires authentication based upon cookies and you have such data.
* You want to detect and exploit SQL injection on such header values.

Either reason brings you to need to send cookies with sqlmap requests, the steps to go through are the following:

* Login to the application with your favourite browser.
* Get the HTTP Cookie from the browser's preferences or from the HTTP proxy screen and copy to the clipboard.
* Go back to your shell and run sqlmap by pasting your clipboard as value of the option `--cookie`.

Note that the HTTP `Cookie` header values are usually separated by a `;` character, **not** by an `&`. sqlmap can recognize these as separate sets of `parameter=value` too, as well as GET and POST parameters. In case that the separation character is other than `;` it can be specified by using option `--cookie-del`.

If at any time during the communication, the web application responds with `Set-Cookie` headers, sqlmap will automatically use its value in all further HTTP requests as the `Cookie` header. sqlmap will also automatically test those values for SQL injection. This can be avoided by providing the switch `--drop-set-cookie` - sqlmap will ignore any coming `Set-Cookie` header.

Vice versa, if you provide a HTTP `Cookie` header with option `--cookie` and the target URL sends an HTTP
`Set-Cookie` header at any time, sqlmap will ask you which set of cookies to use for the following HTTP requests.

There is also an option `--load-cookies` which can be used to provide a special file containing Netscape/wget formatted cookies.

Note that also the HTTP `Cookie` header is tested against SQL injection if the `--level` is set to **2** or above. Read below for details.

### HTTP `User-Agent` header

Option and switch: `--user-agent` and `--random-agent`

By default sqlmap performs HTTP requests with the following `User-Agent` header value:

    sqlmap/1.0-dev-xxxxxxx (http://sqlmap.org)

However, it is possible to fake it with the option `--user-agent` by providing custom User-Agent as the option's argument.

Moreover, by providing the switch `--random-agent`, sqlmap will randomly select a `User-Agent` from the `./txt/user-agents.txt` textual file and use it for all HTTP requests within the session.

Some sites perform a server-side check of HTTP `User-Agent` header value and fail the HTTP response if a valid `User-Agent` is not provided, its value is not expected or is blacklisted by a web application firewall or similar intrusion prevention system. In this case sqlmap will show you a message as follows:

    [hh:mm:20] [ERROR] the target URL responded with an unknown HTTP status code, try to 
    force the HTTP User-Agent header with option --user-agent or --random-agent

Note that also the HTTP `User-Agent` header is tested against SQL injection if the `--level` is set to **3** or above.
Read below for details.

### HTTP `Host` header

Option: `--host`

You can manually set HTTP `Host` header value. By default HTTP `Host` header is parsed from a provided target URL.

Note that also the HTTP `Host` header is tested against SQL injection if the `--level` is set to **5**. Read below for details.

### HTTP `Referer` header

Option: `--referer`

It is possible to fake the HTTP `Referer` header value. By default **no** HTTP `Referer` header is sent in HTTP requests if not explicitly set.

Note that also the HTTP `Referer` header is tested against SQL injection if the `--level` is set to **3** or above. Read below for details.

### Extra HTTP headers

Option: `--headers`

It is possible to provide extra HTTP headers by setting the option `--headers`. Each header must be separated by a newline and it is much easier to provide them from the configuration INI file. You can take a look at the sample `sqlmap.conf` file for such case.

Example against a MySQL target:

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

### HTTP protocol authentication

Options: `--auth-type` and `--auth-cred`

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