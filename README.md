# Linux Ops Notes

My runbook from learning Linux ops. I write my notes from memory, then check and correct them with Claude.

## Contents
- [troubleshooting.md](troubleshooting.md): symptom → layer → first command, plus fixes that worked
- [vim.md](vim.md): vim survival keys
- [log.md](log.md): an ongoing log of ops things I've picked up while tinkering with my own projects

## Resources
- [Syntaxia Linux cheatsheet](https://syntaxiadev.vercel.app/ref/linux-commands): quick syntax lookups (users, find, tar, pipes)
- `<command> --help`: quick summary of the flags (try this first)
- `man <command>`: the full manual. Space = next page, b = back, `/word` + Enter = search, n = next match, q = quit. Search, don't read top to bottom
- Order when stuck: `--help` for a flag → `man` + `/search` for details → Google the exact error message