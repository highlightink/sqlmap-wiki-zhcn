## 访问 Windows 注册表

当后端 DBMS 是 MySQL，PostgreSQL 或 Microsoft SQL Server 并且 Web应用程序支持堆查询时，可以访问 Windows 注册表。此外，会话用户必须具有访问它所需的权限。

### 读取 Windows 注册表键值

开关：`--reg-read`

使用此开关可以读取注册表键值。

### 写入 Windows 注册表键值

开关：`--reg-add`

使用此开关可以写入注册表键值。

### 删除 Windows 注册表项

开关：`--reg-del`

使用此开关可以删除注册表项。

### 辅助注册表选项

选项：`--reg-key`，`--reg-value`，`--reg-data` 和 `--reg-type`

这些选项可用于提供正确运行 `--reg-read`，`--reg-add` 和 `--reg-del` 等开关所需的数据。所以，你可以在命令提示符下使用它们作为程序参数，而不是在询问时提供注册表项信息。

使用 `--reg-key` 选项指定 Windows 注册表项路径，`--reg-value` 提供注册表项的名称，`--reg-data` 提供注册表键值数据，而 `--reg-type` 选项指定注册表键值的类型。

添加注册表项配置单元的示例命令行如下：

```
$ python sqlmap.py -u http://192.168.136.129/sqlmap/pgsql/get_int.aspx?id=1 --r\
eg-add --reg-key="HKEY_LOCAL_MACHINE\SOFTWARE\sqlmap" --reg-value=Test --reg-ty\
pe=REG_SZ --reg-data=1
```