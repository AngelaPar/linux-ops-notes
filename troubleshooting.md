# Symptom -> first check
| Symptom | Layer | First command |
|---|---|---|
| hangs / timeout | 1 (NAME) or 2 (NETWORK) | getent hosts site.test / sudo iptables ...  |
| refused | 6 (FILE) or 7 (DATABASE) |   |
| 403 | 6 (FILE) or 7 (DATABASE) | |
| 404 | 6 (FILE) | |
| 500 | 6 (FILE) or 7 (DATABASE) or 8 (RESOURCES)  | run logs -30 smth smth  |
| 502 | 5 (APP)  | sudo nginx -t |
| 200 but wrong page | 5 (APP) | cat /etc/resources/sites-available/site.test |

# Error message -> meaning
- "Access denied for user" - incorrect password or user
- "Permission denied (13)" - file permissions were set to deny an action 
- "No such file or directory" (DB socket) - missing or incorrect files/folders 
- "invalid number of arguments" (nginx) - syntax error/typos
- "Welcome to nginx!" - web server is running but possible typo in site's config
- spins forever + no new log line - firewall blocking visitors

# Fixes that worked



# What is it akin to / elaborations
- reload vs restart - reload when chrome is open vs restart when chrom is closed 
- dig vs getent - what the public sees / what a particular machine connects to
- DROP vs REJECT - drop is blocking / reject is denying
- MX vs SMTP - mx is for mail forwarding, smtp is for both
