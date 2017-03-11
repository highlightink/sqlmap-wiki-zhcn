## Windows registry access

It is possible to access Windows registry when the back-end database management system is either MySQL, PostgreSQL or Microsoft SQL Server, and when the web application supports stacked queries. Also, session user has to have the needed privileges to access it. 

### Read a Windows registry key value

Switch: `--reg-read`

Using this switch you can read registry key values.

### Write a Windows registry key value

Switch: `--reg-add`

Using this switch you can write registry key values.

### Delete a Windows registry key

Switch: `--reg-del`

Using this switch you can delete registry keys.

### Auxiliary registry options

Options: `--reg-key`, `--reg-value`, `--reg-data` and `--reg-type`

These options can be used to provide data needed for proper running of switches `--reg-read`, `--reg-add` and  `--reg-del`. So, instead of providing registry key information when asked, you can use them at command prompt as program arguments. 

With `--reg-key` option you specify used Windows registry key path, with `--reg-value` value item name inside provided key, with `--reg-data` value data, while with `--reg-type` option you specify type of the value item.

A sample command line for adding a registry key hive follows:

```
$ python sqlmap.py -u http://192.168.136.129/sqlmap/pgsql/get_int.aspx?id=1 --r\
eg-add --reg-key="HKEY_LOCAL_MACHINE\SOFTWARE\sqlmap" --reg-value=Test --reg-ty\
pe=REG_SZ --reg-data=1
```