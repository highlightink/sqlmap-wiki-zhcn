# 截图

详细输出（选项 `-v` 设置为 `3`）：

![Verbose output set to 3](images/sqlmap_verbose_3.png)

连接三个篡改脚本以模糊注入 SQL payloads（选项 `--tamper` 设置为 `between,randomcase,space2comment`）：

![Tamper scripts in action](images/sqlmap_tamper_in_action.png)

破解导出的数据库用户密码哈希（开关 `--passwords`）：

![Users' password hashes cracking](images/sqlmap_cracking_password_hashes.png)

枚举数据表的列（开关 `--columns`）：

![Database table's columns dump](images/sqlmap_enumerating_columns.png)

助记符（选项 `-z` 设置为 `"flu,bat,tec=B"`）：

![Mnemonics usage](images/sqlmap_mnemonics.png)

只有在使用启发式检测时才进行彻底的测试（开关 `--smart`）：

![Smart mode](images/sqlmap_smart.png)

DNS 渗出技术（选项 `--dns-domain`）：

![DNS exfiltration technique](images/sqlmap_dns_exfiltration.png)

识别 WAF/IDS/IPS 防护（开关 `--identify-waf`）：

![Identify WAF/IDS/IPS protection](images/sqlmap_identify_waf.png)

HTTP 参数污染（开关 `--hpp`）：

![HTTP parameter pollution](images/sqlmap_hpp.png)

将数据表复制到本地 SQLite3 数据库（选项 `--dump-format` 设置为 `SQLITE`）：

![Replicated table](images/sqlmap_replicate_result.png)

将数据表导出为 HTML 格式（选项 `--dump-format` 设置为 `HTML`）：

![Dumped table to HTML](images/sqlmap_dump_html.png)

OS pwn 模式（Meterpreter）（开关 `--os-pwn`）：

![OS pwn mode](images/sqlmap_os_pwn.png)

OS shell 模式（开关 `--os-shell`）：

![SQL shell mode](images/sqlmap_os_shell.png)

SQL shell 模式（开关 `--sql-shell`）：

![SQL shell mode](images/sqlmap_sql_shell.png)

向导模式（开关 `--wizard`）：

![Wizard mode](images/sqlmap_wizard.png)