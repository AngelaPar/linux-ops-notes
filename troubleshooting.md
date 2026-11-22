# Symptom -> first check
| Symptom | Layer | First command |
|---|---|---|
| hangs / timeout | 1 (NAME) or 2 (NETWORK) | `getent hosts site.test` / `sudo iptables -L INPUT -n --line-numbers` |
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

# What is it akin to / elaborations
- reload vs restart - reload when Chrome is open vs restart when Chrome is closed 
- dig vs getent - what DNS says vs what this machine actually uses
- DROP vs REJECT - drop silently bins it (hang) / reject says no straight away (refused)
- MX vs SMTP - MX is your letterbox (receiving) / SMTP is the post office (sending)
- `x` on a folder - the door handle (changing a folder's permissions with the wrong code, like 644, locks everyone out)
