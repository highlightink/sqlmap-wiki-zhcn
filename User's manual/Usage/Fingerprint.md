## Fingerprint

### Extensive database management system fingerprint

Switches: `-f` or `--fingerprint`

By default the web application's back-end database management system fingerprint is handled automatically by sqlmap. Just after the detection phase finishes and the user is eventually prompted with a choice of which vulnerable parameter to use further on, sqlmap fingerprints the back-end database management system and continues on with the injection by knowing which SQL syntax, dialect and queries to use to proceed with the attack within the limits of the database architecture. 

If for any instance you want to perform an extensive database management system fingerprint based on various techniques like specific SQL dialects and inband error messages, you can provide the switch `--fingerprint`. sqlmap will perform a lot more requests and fingerprint the exact DBMS version and, where possible, operating system, architecture and patch level. 

If you want the fingerprint to be even more accurate result, you can also provide the switch `-b` or `--banner`. 