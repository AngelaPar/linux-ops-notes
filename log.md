# Learning Log

Ops things I've learned while tinkering with my own projects. A work in progress.

## Day 1: November 18, 2026, 10:29pm
- I just found out of one TXT's main use (aside from mail security) is so that google can verify that a domain belongs to you, and also how SSL/TLS has 2 modes (among the 4) that shouldnt be overlooked, the Full vs Full (strict) mode for SSL/TLS.  DNS Proxy for that domain or subdomain could be working seemingly fine in the surface for a SSL on full mode, but what you should be wary of is the fact that it will also just accept expired or invalid certificates. So once you can ensure that a site is working, switch on DNS proxy on cloudflare so that cloudflare shows their IP instead of your server's. and ensure that SSL/TLS mode is set to Full (strict) to ensure that cloudflare only communicates with my server when the SSL certificate is valid. Even though the user-to-Cloudflare connection is otherwise secured, setting SSL deliberately to Full (strict) mode is ensures that server-to-Cloudflare connection only accepts valid SSL certificates.

## Day 2: November 20, 2026, 10:46am
- vim shortcuts (writing my texts so far with vim to get used to the commands). Went through vimtutor and saved the keys I actually use in [vim.md](vim.md).
- 9:58pm: fixed 4 nginx config bugs in 38s using vim.