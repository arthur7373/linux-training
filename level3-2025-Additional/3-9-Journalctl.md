# Linux Network Server (level 3) <br /> Լինուքս ցանցային սերվեր (փուլ 3)

## Journalctl 

systemd journals are your linux admin's best friends in troubleshooting the issues.
Modern journals are controlled by **journalctl**.   

journalctl may be used to query the contents of the systemd journal

If called without parameters, it will show the full contents of the journal, starting with the oldest entry collected.

Let's see how much records we have up to now

```bash
journalctl --no-pager | wc -l
```

Options:

* `-t` specific process name to show (can be specified multiple times)
* `-f` follow logs in real-time (the same as `tail -f`) - live debugging.
* `-n` number of lines to show
* `-p` show only messages matching log priorities (as documented in `syslog`, i.e.  "emerg","alert","crit","err","warning","notice","info","debug").

Examples:

Let's run this in one terminal and the login with second

```bash
journalctl -t sshd -t sudo -f 
```

Jump to the end of the log

```bash
journalctl -t sshd -t sudo -e
```

Show only last 5 lines

```bash
journalctl -t sshd -t sudo -n5
```

Show only messages of `err` log level

```bash
journalctl -t sshd -t sudo -n5 -p err
```

Specify time range (if no date is specified, today is assumed)

```bash
journalctl --no-pager -t sshd -t sudo -p err --since "2025-09-23 19:00" --until "19:10"
```



