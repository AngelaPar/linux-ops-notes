# The ladder
A web request travels down these layers. Find the FIRST layer that's broken. Read the log before changing anything.

```
 1 NAME        does the name point here?      getent hosts <site>             /etc/hosts
 2 NETWORK     can packets get in?            curl -v -m 5 (hangs = dropped)  sudo iptables -L INPUT -n --line-numbers
 3 SERVICE     is the web server running?     systemctl status nginx|apache2  sudo ss -tlnp
 4 WEB SERVER  config valid? right site?      sudo nginx -t | apache2ctl -t   /etc/nginx|apache2/sites-enabled/
 5 APP         is PHP-FPM up? right socket?   systemctl status php*-fpm       ls /run/php/
 6 FILES       can www-data read it?          ls -l ; namei -l <path>         DocumentRoot / root folder
 7 DATABASE    up? right password?            systemctl status mariadb        app config file ; sudo mariadb
 8 RESOURCES   disk / CPU / memory / limits   df -h ; top ; free -h           systemctl cat <svc> (LimitNOFILE)
 9 SCHEDULED   keeps coming back?             ls /etc/cron.d ; systemctl list-timers
```

**Logs (read these first):**
- `tail -n 30 /var/log/nginx/error.log` or `/var/log/apache2/error.log` (a site can have its OWN log: check `ErrorLog` in its config)
- `journalctl -u <svc> -n 30 --no-pager` (why a service won't start)

**Status code -> layer:** hangs/timeout -> 1 or 2 · refused -> 3 · 404 -> 4 or 6 · 403 -> 4 or 6 · 502 -> 5 · 500 -> 6, 7 or 8 (read the log) · 200 but wrong page -> 3 or 4

**Always:** reproduce -> walk the ladder -> read the log -> change ONE thing -> prove it (curl again)

**Before editing ANY config:** `sudo cp <file> <file>.bak` (undo = `sudo cp <file>.bak <file>`) -> edit -> test (`nginx -t` / `apache2ctl -t`) -> reload -> curl

# Reproduce first
- `hostname` / `whoami` / `cat /etc/os-release` - which box, which user, which Linux
- `df -h` - always, 5 seconds, catches a full disk before you waste time
- `curl -I http://site.test` - what status code?
- `curl -v -m 5 http://site.test` - if it hangs, which IP is it "Trying"?
- `curl -s http://site.test` - read the page (a 200 can still be broken)
- `systemctl status nginx apache2` - which web server is it?

# Symptom -> first check
| Symptom | Layer | First command |
|---|---|---|
| hangs / timeout | 1 (NAME) or 2 (NETWORK) | `curl -v -m 5 http://site.test` -> `getent hosts site.test` / `sudo iptables -L INPUT -n --line-numbers` |
| refused | 3 (SERVICE) | `systemctl status nginx` / `sudo ss -tlnp`  |
| 403 | 4 (WEB SERVER) or 6 (FILES) | `tail -n 30 /var/log/nginx/site.test.error.log` -> `namei -l <path>` |
| 404 | 4 (WEB SERVER) or 6 (FILES) | error log -> check `root` path in the config |
| 500 | 6 (FILES), 7 (DATABASE) or 8 (RESOURCES)  | `tail -n 30 /var/log/nginx/site.test.error.log` & `curl -s` to read the page |
| 502 | 5 (APP) | `systemctl status php8.3-fpm` / `ls /run/php/` |
| 200 but wrong page | 3 (SERVICE) or 4 (WEB SERVER) | `cat /etc/nginx/sites-available/site.test` (check `server_name`) |

# Error message -> meaning
- "Access denied for user" - wrong DB password or user
- "Permission denied (13)" - Linux file/folder permissions block www-data -> `namei -l` 
- "No such file or directory" (DB socket) - the database isn't running
- "invalid number of arguments" (nginx) - syntax error, check line N AND the line above (missing `;`)
- "Welcome to nginx!" - no `server_name` matched, so the default site answered
- "directory index of ... is forbidden" - `index` line missing `index.php`
- "Address already in use" - another program took the port -> `sudo ss -tlnp`
- spins forever + no new log line - firewall blocking visitors
- comes back then dies every minute - a cron job -> `ls /etc/cron.d`

# Fixes that worked
- `sudo systemctl enable --now <svc>` - start now + on boot
- `sudo systemctl disable --now <svc>` - stop now + not on boot
- `sudo chmod 644 <file>` / `sudo chmod 755 <folder>` - never 777
- `sudo nginx -t` -> `sudo systemctl reload nginx` (restart if it's down)
- `sudo iptables -D INPUT 1` - delete firewall rule #1
- `sudo du -sh /*` -> zoom in -> ask before deleting
- `sudo mv /etc/cron.d/<file> /root/<file>.disabled` - disable a cron job, keep evidence
- fix `/etc/hosts` -> `127.0.0.1 site.test www.site.test`
- `sudo lsof <file>` - who has this file open / who's writing to it -> `kill <PID>` -> `tail -n 3 <file>` twice to prove it stopped
- `ps aux | grep -i <word>` - find a process by name (`ps auxww` shows full commands, `ps auxf` cuts them off)

# What is it akin to / elaborations
- reload vs restart - reload when Chrome is open vs restart when Chrome is closed 
- dig vs getent - what DNS says vs what this machine actually uses
- DROP vs REJECT - drop silently bins it (hang) / reject says no straight away (refused)
- MX vs SMTP - MX is your letterbox (receiving) / SMTP is the post office (sending)
- `x` on a folder - the door handle (changing a folder's permissions with the wrong code, like 644, locks everyone out)

# If it's Apache, not nginx
| | nginx | Apache |
|---|---|---|
| service | `systemctl status nginx` | `systemctl status apache2` |
| test config | `sudo nginx -t` | `sudo apache2ctl -t` |
| site config | `/etc/nginx/sites-available/` | `/etc/apache2/sites-available/` |
| enable a site | symlink into `sites-enabled` | `sudo a2ensite site.conf` -> reload |
| error log | `/var/log/nginx/error.log` | `/var/log/apache2/error.log` |
| PHP | php-fpm socket | often `mod_php` (no socket) or php-fpm |

# If it's in Docker
- Dockerfile (blueprint) -> `docker build` -> image (the product) -> `docker run` -> container (the product in use). One image can run as many containers
- `docker ps -a` - what's running, what's stopped
- `docker logs <name>` - that container's logs
- `docker restart <name>`
- `docker exec -it <name> bash` - shell inside it