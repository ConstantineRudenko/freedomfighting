# Freedom Fighting scripts

This repository contains scripts which may come in handy during your freedom fighting activities. It will be updated
occasionally, when I find myself in need of something I can't find online.
Everything here is distributed under the terms of the [GPL v3 License](https://www.gnu.org/licenses/gpl.html).

Contributions and pull requests are very welcome.

## NoJail.py

A simple log cleaner which removes incriminating entries in:

* `/var/run/utmp`, `/var/log/wtmp`, `/var/log/btmp` (controls the output of the `who`, `w` and `last` commands)
* `/var/log/lastlog` (controls the output of the `lastlog` command)
* `/var/**/*.log` (.log.1, .log.2.gz, etc. included)
* Any additional file or folder designated by the user

Entries are deleted based on an IP address and/or associated hostname.

Special care is taken to avoid breaking file descriptors while tampering with logs. This means logs continue to be
written to after they've been tampered with, making the cleanup a lot less conspicuous. All the work takes place in a
*tmpfs* drive and any files created are wiped securely.

**Warning:** The script has only been tested on Linux and will not be able to clean UTMP entries on other Unix flavors.

### Usage:
```
usage: nojail.py [-h] [--user USER] [--ip IP] [--hostname HOSTNAME]
                    [--verbose] [--check]
                    [log_files [log_files ...]]

   Stealthy log file cleaner.

   positional arguments:
     log_files             Specify any log files to clean in addition to
                           /var/**/*.log.

   optional arguments:
     -h, --help            show this help message and exit
     --user USER, -u USER  The username to remove from the connexion logs.
     --ip IP, -i IP        The IP address to remove from the logs.
     --hostname HOSTNAME   The hostname of the user to wipe. Defaults to the rDNS
                           of the IP.
     --verbose, -v         Print debug messages.
     --check, -c           If present, the user will be asked to confirm each
                           deletion from the logs.
     --daemonize, -d       Start in the background and delete logs when the
                           current session terminates. Implies --self-delete.
     --self-delete, -s     Automatically delete the script after its execution.
```

By default, if no arguments are given, the script will try to determine the IP address to scrub based on the
`SSH_CONNECTION` environment variable. Any entry matching the reverse DNS of that IP will be removed as well.

#### Basic example:

```
./nojail.py --user root --ip 151.80.119.32 /etc/app/logs/access.log --check
```
...will remove all entries for the user root where the IP address is 151.80.119.32 or the hostame is `manalyzer.org`.
The user will also be prompted before deleting each record because of the `--check` option. Finally, the file
`/etc/app/logs/access.log` will be processed in addition to all the default ones.

If folders are given as positional arguments (`/etc/app/logs/` for instance), the script will recursively crawl them and
clean any file with the `.log` extension (*.log.1, *.log.2.gz, etc. included).

#### Daemonizing the script

```
./nojail.py --daemonize
```
Assuming this is run from an SSH connexion, this command will delete all logs pertaining to the current user's activity
with the detected IP address and hostname right after the connexion is closed. This script will subsequently
automatically delete itself.
Please bear in mind that you won't have any opportunity to receive error messages from the application. You are encouraged
to try deleting the logs once before spawning the demon to make sure that the arguments you specified are correct.

### Sample output:
```
root@proxy:~# ./nojail.py
[ ] Cleaning logs for root (XXX.XXX.XXX.XXX - domain.com).
[*] 2 entries removed from /var/run/utmp!
[*] 4 entries removed from /var/log/wtmp!
[ ] No entries to remove from /var/log/btmp.
[*] Lastlog set to 2017-01-09 17:12:49 from pts/0 at lns-bzn-37-79-250-104-19.adsl.proxad.net
[*] 4 lines removed from /var/log/nginx/error.log!
[*] 11 lines removed from /var/log/nginx/access.log!
[*] 4 lines removed from /var/log/auth.log!
```

### Disclaimer
This script is provided without any guarantees.
Don't blame me it doesn't wipe all traces of something you shouldn't have done in the first place.

### Donations
These scripts are 100% free. I do like Bitcoins though, so if you want to send some my way, here's an address you can
use: ```19wFVDUWhrjRe3rPCsokhcf1w9Stj3Sr6K```
Feel free to drop me a line if you donate to the project, so I can thank you personally!

### Contact
[![](http://manalyzer.org/static/mail.png)](mailto:justicerage *at* manalyzer.org)
[![](http://manalyzer.org/static/twitter.png)](https://twitter.com/JusticeRage)
[![](http://manalyzer.org/static/gpg.png)](https://pgp.mit.edu/pks/lookup?op=vindex&search=0x40E9F0A8F5EA8754)