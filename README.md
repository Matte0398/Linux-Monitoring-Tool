# Linux-Process-Monitoring-Tool

Bash script that checks whether one or more processes are running on a Linux system.

The script supports advanced filtering and can return both human-readable and JSON output.

## Features

- Check process existence
- Monitor one or more processes
- Support alternative process names
- Alias support
- User filtering
- Parent PID filtering
- Minimum and maximum process count thresholds
- JSON output
- File-based process list
- Command-line process definition

## Process format

```bash
proc=process_name[,alternative_name]:alias=label:user=username:ppid=parent_pid:min=<min>:max=<max>
```

## Usage

``` bash
Monitor a single process:

  ./process_monitor.sh -P "proc=nginx"

Monitor multiple processes:

  ./process_monitor.sh -P "proc=syslogd,rsyslogd,syslog-ng:alias=syslog"

Monitor a process and define a custom alias and owner:

  ./process_monitor.sh -P "proc=mysql:alias=database:user=mysql"

Monitor multiple process definitions and return output in JSON format:

  ./process_monitor.sh -P "proc=nginx%proc=mysql:alias=database:user=mysql" -J

Load process definitions from a file:

  ./process_monitor.sh -F /tmp/processes.txt

    File content:

     proc=nginx
     proc=mysql:alias=database
     proc=mysql:alias=database:user=mysql
     proc=syslogd,rsyslogd,syslog-ng:alias=syslog
```

### Output

#### Standard output

``` bash

./process_monitor.sh -P "proc=postgres:ppid=1%proc=zabbix_server:alias=zbxServer:user=root%proc=mysql%proc=/usr/sbin/rsyslogd:min=1%proc=crond:min=2:max=3"

===========================================
         PROCESS MONITORING REPORT
===========================================

[OK] postgres: ACTIVE
    - Process count: 1
    - Command: /usr/pgsql-14/bin/postmaster -D /var/lib/pgsql/14/data/
    - PPID: 1
    - Min: 1
    - Max: 1

[KO] zbxServer: NOT FOUND
    - Searched for user: root

[KO] mysql: NOT FOUND

[OK] /usr/sbin/rsyslogd: ACTIVE
    - Process count: 1
    - Command: /usr/sbin/rsyslogd -n
    - Min: 1
    - Max: 1

[KO] crond: OUT OF RANGE
    - Process count: 1
    - Command: /usr/sbin/crond -n
    - Min: 2
    - Max: 3

===========================================
      SUMMARY: 2 active - 3 not found
===========================================
```

#### JSON output

``` bash

./process_monitor.sh -P "proc=postgres:ppid=1%proc=zabbix_server:alias=zbxServer:user=root%proc=mysql%proc=/usr/sbin/rsyslogd:min=1%proc=crond:min=2:max=3" -J

[{"ALIAS":"postgres","USER":"","STATUS":"1","PROCESSES":"/usr/pgsql-14/bin/postmaster -D /var/lib/pgsql/14/data/","COUNT":"1","MIN":"1","MAX":"1"},{"ALIAS":"zbxServer","USER":"root","STATUS":"0","PROCESSES":"","COUNT":"0","MIN":"1","MAX":"1"},{"ALIAS":"mysql","USER":"","STATUS":"0","PROCESSES":"","COUNT":"0","MIN":"1","MAX":"1"},{"ALIAS":"rsyslogd","USER":"","STATUS":"1","PROCESSES":"/usr/sbin/rsyslogd -n","COUNT":"1","MIN":"1","MAX":"1"},{"ALIAS":"crond","USER":"","STATUS":"0","PROCESSES":"/usr/sbin/crond -n","COUNT":"1","MIN":"2","MAX":"3"}]
