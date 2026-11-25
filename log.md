# Learning Log

Ops things I've learned while tinkering with my own projects. A work in progress.

## Day 1: November 18, 2026, 10:29pm
- I just found out of one TXT's main use (aside from mail security) is so that google can verify that a domain belongs to you, and also how SSL/TLS has 2 modes (among the 4) that shouldnt be overlooked, the Full vs Full (strict) mode for SSL/TLS.  DNS Proxy for that domain or subdomain could be working seemingly fine in the surface for a SSL on full mode, but what you should be wary of is the fact that it will also just accept expired or invalid certificates. So once you can ensure that a site is working, switch on DNS proxy on cloudflare so that cloudflare shows their IP instead of your server's. and ensure that SSL/TLS mode is set to Full (strict) to ensure that cloudflare only communicates with my server when the SSL certificate is valid. Even though the user-to-Cloudflare connection is otherwise secured, setting SSL deliberately to Full (strict) mode is ensures that server-to-Cloudflare connection only accepts valid SSL certificates.

## Day 2: November 20, 2026, 10:46am
- vim shortcuts (writing my texts so far with vim to get used to the commands). Went through vimtutor and saved the keys I actually use in [vim.md](vim.md).
- 9:58pm: fixed 4 nginx config bugs in 38s using vim.

## Day xx: November 25, 2026, 9:35pm

Haven't been writing on these logs during the past few days because I was prioritising break-fix servers. Just installed fedora linux today instead of just practicing through a terminal to get used to the graphic interface. Spent most of the day focusing more on the theory bits rather than on actual troubleshooting. Covered common directories such as /, /bin, /etc, /home, /opt, /usr, /var. Applications that are not part of the base OS are typically installed in either usr/local, or /opt. Useful to keep these directories in mind when encountering or investigating disk space-type errors.

Also, while I was fiddling w my own servers over the past few days I realised there are lowk a lot of vulnerabilities that could've been easily exploited, along with features that aren't enabled as  default (even though I assumed they would be). That absolutely baffled me. Scary unknown unknowns, but also it serves as a reminder that infrastructure n ops work is crucial for knowing what parts of your server can be exploited.  
