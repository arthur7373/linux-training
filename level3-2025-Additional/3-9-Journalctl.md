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
* `-u` show only messages matching some systemd **unit** (such as a _service_ unit)
* `-g` use `grep` to filter messages

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

Show logs from the last 1 minute (`-S` is the same as `--since` )

```bash
journalctl -S -1m
```

Show `sshd.service` error logs from the last 30 minutes

```bash
journalctl -u sshd.service -p err --since "30 min ago"
```

Show specific error message related to the `sshd` process from yesterday

```bash
journalctl --no-pager -t sshd -S yesterday -g 
```

Show failed SSH logins since yesterday
```bash
journalctl -t sshd -S yesterday -g "failed|invalid"
```


Quick overview of system state since the last boot

* `-p 3..0` - from err (3) up to emerg (0) (reverse order puts most severe first)
* `-b`  - Show messages from current boot.

```bash
journalctl -p 3..0 -b --no-pager
```


#### PRACTICE

1. Run `journalctl` to: 
* show only `err` log messages from process `sshd` 
* from yesterday
* no pager to be used

2. Modify command to show  specific error message "Failed password"

3. Modify command to count number of messages

4. Modify command to follow new messages in real-time for live debugging 


### Maintenance and Persistence

The journal is stored in `/var/log/journal/`

Keep an eye on its size.

```bash
journalctl --disk-usage
```

Enable Persistence. 
By default, some distributions don't persist journals across reboots.

Check

```bash
journalctl -p 3..0 -b -1 --no-pager
```
(you may get `no persistent journal was found`)

```bash
ls -l /var/log/journal
```
(you may get `No such file or directory`)





