---
tags:
  - linux
  - homelab
  - htb
date: 2026-11-21
status: on progress
---

## Objective
- Set a static IP address 
- make users, groups , and permissions for them . 

## Environment
- Server machine : `Ubuntu Vbox`
- user machine : `kali linux `
 
## Steps

- [ ] Set a static IP (NetworkManager)
- [ ] Create users: admin, dev, intern
- [ ] Create groups and add users to them
- [ ] Give `dev` one specific command in `sudoers` (least privilege)
- [ ] Create a shared folder with SGID and the sticky bit
- [ ] Verify permissions with `ls -l` and test as each user
- [ ] Take snapshot: `baseline-before-hardening`
## Security notes
What could go wrong here? What did I harden?

## Troubleshooting
Errors I hit and how I fixed them.

## What I learned
- Network manger is the part that is responsible for 