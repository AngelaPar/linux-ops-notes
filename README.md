# Linux Ops Notes
My runbook from learning more about Linux ops. Written by me, no AI.

## Day 1: November 18, 2026, 10:29pm
- I just found out of one TXT's main use (aside from mail security) is so that google can verify that a domain belongs to you, and also how SSL/TLS has 2 modes (among the 4) that shouldnt be overlooked, the Full vs Full (strict) mode for SSL/TLS.  DNS Proxy for that domain or subdomain could be working seemingly fine in the surface for a SSL on full mode, but what you should be wary of is the fact that it will also just accept expired or invalid certificates. So once you can ensure that a site is working, switch on DNS proxy on cloudflare so that cloudflare shows their IP instead of your server's. and ensure that SSL/TLS mode is set to Full (strict) to ensure that cloudflare only communicates with my server when the SSL certificate is valid. Even though the user-to-Cloudflare connection is otherwise secured, setting SSL deliberately to Full (strict) mode is ensures that server-to-Cloudflare connection only accepts valid SSL certificates.        

## Day 2: November 20, 2026, 10:46am
vim shortcuts (writing my texts so far with vim to get used to the commands).
cursor - h (left), j (down), k (up), l (right)
starting viw from shell - vim FILENAME <enter>
exiting vim and trashing all changes: <ESC> :q! <ENTER>
exiting vim and saving all changes: <ESC> :wq <ENTER>
deleting a character at the cursor - x
to insert before cursor - i
to append after the line - a
to delete from the cursor up to the next word - dw
to delete from the cursor up to the end of the word - de
to delete from the cursor to the end of the line - d$
to delete a whole line type - dd
to repeat a motion prepend it with a number (e.g) - 1w, 2w or 3w or 4w and so on
the format for a change command is: operator [number] motion
operator - is what to do, such as d for delete
[number] - optional count to repeat the motion
motion - moves over the text to operate on, such as w(word), e(end of word), $(end of the line), etc.
to move to the start of line - 0
to undo previous actions - u
to undo ALL changes on a line - U
to undo the undos (redo) - CTRL-R
