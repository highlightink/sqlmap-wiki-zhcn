## Optimization

These switches can be used to optimize the performance of sqlmap.

### Bundle optimization

Switch: `-o`

This switch is an alias that implicitly sets the following options and switches:

* `--keep-alive`
* `--null-connection`
* `--threads=3` if not set to a higher value.


Read below for details about each switch.

### Output prediction

Switch: `--predict-output`

This switch is used in inference algorithm for sequential statistical prediction of characters of value being retrieved. Statistical table with the most promising character values is being built based on items given in `txt/common-outputs.txt` combined with the knowledge of current enumeration used. In case that the value can be found among the common output values, as the process progresses, subsequent character tables are being narrowed more and more. If used in combination with retrieval of common DBMS entities, as with system table names and privileges, speed up is significant. Of course, you can edit the common outputs file according to your needs if, for instance, you notice common patterns in database table names or similar.

Note that this switch is not compatible with `--threads` switch.

### HTTP Keep-Alive

Switch: `--keep-alive`

This switch instructs sqlmap to use persistent HTTP(s) connections.

Note that this switch is incompatible with `--proxy` switch.

### HTTP NULL connection

Switch: `--null-connection`

There are special HTTP request types which can be used to retrieve HTTP response's size without getting the HTTP body. This knowledge can be used in blind injection technique to distinguish `True` from `False` responses. When this switch is provided, sqlmap will try to test and exploit two different _NULL connection_ techniques: `Range` and `HEAD`. If any of these is supported by the target web server, speed up will come from the obvious saving of used bandwidth.

These techniques are detailed in the white paper [Bursting Performances in Blind SQL Injection - Take 2 (Bandwidth)](http://www.wisec.it/sectou.php?id=472f952d79293).

Note that this switch is incompatible with switch `--text-only`.

### Concurrent HTTP(S) requests

Option: `--threads`

It is possible to specify the maximum number of concurrent HTTP(S) requests that sqlmap is allowed to do.
This feature relies on [multi-threading](http://en.wikipedia.org/wiki/Multithreading) concept and inherits both its pro and its cons.

This features applies to the brute-force switches and when the data fetching is done through any of the blind SQL injection techniques. For the latter case, sqlmap first calculates the length of the query output in a single thread, then starts the multi-threading. Each thread is assigned to retrieve one character of the query output. The thread ends when that character is retrieved - it takes up to 7 HTTP(S) requests with the bisection algorithm implemented in sqlmap.

The maximum number of concurrent requests is set to **10** for performance and site reliability reasons.

Note that this option is not compatible with switch `--predict-output`.