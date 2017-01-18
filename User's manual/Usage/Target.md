## Target

At least one of these options has be provided to set the target(s).

### Direct connection to the database

Option: `-d`

Run sqlmap against a single database instance. This option accepts a connection string in one of following forms: 

* `DBMS://USER:PASSWORD@DBMS_IP:DBMS_PORT/DATABASE_NAME` (MySQL, Oracle, Microsoft SQL Server, PostgreSQL, etc.)
* `DBMS://DATABASE_FILEPATH` (SQLite, Microsoft Access, Firebird, etc.)

For example:

```
$ python sqlmap.py -d "mysql://admin:admin@192.168.21.17:3306/testdb" -f --bann\
er --dbs --users
```

### Target URL

Option: `-u` or `--url`

Run sqlmap against a single target URL. This option requires a target URL in following form: 

`http(s)://targeturl[:port]/[...]`

For example:

```
$ python sqlmap.py -u "http://www.target.com/vuln.php?id=1" -f --banner --dbs -\
-users
```

### Parse targets from Burp or WebScarab proxy logs

Option: `-l`

Rather than providing a single target URL, it is possible to test and inject against HTTP requests proxied through [Burp proxy](http://portswigger.net/suite/) or 
[WebScarab proxy](http://www.owasp.org/index.php/Category:OWASP_WebScarab_Project). This option requires an argument which is the proxy's HTTP requests log file.

### Parse targets from remote sitemap(.xml) file

Option: `-x`

A sitemap is a file where web admins can list the web page locations of their site to tell search engines about the site content's organization. You can provide a sitemap's location to sqlmap by using option `-x` (e.g. `-x http://www.target.com/sitemap.xml`) so it could find usable target URLs for scanning purposes.

### Scan multiple targets enlisted in a given textual file

Option: `-m`

Providing list of target URLs enlisted in a given bulk file, sqlmap will scan 
each of those one by one.

Sample content of a bulk file provided as an argument to this option:

    www.target1.com/vuln1.php?q=foobar
    www.target2.com/vuln2.asp?id=1
    www.target3.com/vuln3/id/1*

### Load HTTP request from a file

Option: `-r`

One of the possibilities of sqlmap is loading of raw HTTP request from a textual file. That way you can skip usage of a number of other options (e.g. setting of cookies, POSTed data, etc).

Sample content of a HTTP request file provided as an argument to this option:

    POST /vuln.php HTTP/1.1
    Host: www.target.com
    User-Agent: Mozilla/4.0
    
    id=1

Note that if the request is over HTTPS, you can use this in conjunction with switch `--force-ssl` to force SSL connection to 443/tcp. Alternatively, you can append `:443` to the end of the `Host` header value.

### Process Google dork results as target addresses

Option: `-g`

It is also possible to test and inject on GET parameters based on results of your Google dork.

This option makes sqlmap negotiate with the search engine its session cookie to be able to perform a search, then sqlmap will retrieve Google first 100 results for the Google dork expression with GET parameters asking you if you want to test and inject on each possible affected URL.

For example:

```
$ python sqlmap.py -g "inurl:\".php?id=1\""
```

### Load options from a configuration INI file

Option: `-c`

It is possible to pass user's options from a configuration INI file, an example is `sqlmap.conf`.

Note that if you provide other options from command line, those are evaluated when running sqlmap and overwrite those provided in the configuration file.