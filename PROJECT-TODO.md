---
tags: [linux, project, homelab]
status: in-progress
---

# Linux Server Lab: Build, Harden, Attack, Audit

> [!tip] Rules
> - Take a **snapshot** before every phase.
> - Write every command you run in this note. It becomes your documentation.
> - Only attack your own lab VM, on a host-only network.

## Phase 0: Setup
- [ ] Install a hypervisor (VirtualBox, VMware, or KVM) on Kali
- [ ] Download the Ubuntu Server ISO
- [ ] Create the VM (2 CPU, 2 GB RAM, 20 GB disk, plus a 2 GB second disk)
- [ ] Set the network to host-only (or internal)
- [ ] Install Ubuntu Server with OpenSSH enabled
- [ ] Create a GitHub repo for the project
- [ ] Take snapshot: `clean-install`

## Phase 1: Foundation
- [ ] Set a static IP (check Netplan: `/etc/netplan/`)
- [ ] Update the system: `apt update && apt dist-upgrade`
- [ ] Create users: admin, dev, intern
- [ ] Create groups and add users to them
- [ ] Give `dev` one specific command in `sudoers` (least privilege)
- [ ] Create a shared folder with SGID and the sticky bit
- [ ] Verify permissions with `ls -l` and test as each user
- [ ] Take snapshot: `baseline-before-hardening`

## Phase 2: Services
- [ ] Connect to the VM from Kali over SSH
- [ ] Generate an SSH key pair and copy it with `ssh-copy-id`
- [ ] Edit `/etc/ssh/sshd_config`: disable password login
- [ ] Edit `/etc/ssh/sshd_config`: disable root login
- [ ] Restart SSH and test from a second terminal before closing the first
- [ ] Install and configure fail2ban
- [ ] Install Apache or Nginx and publish a simple page
- [ ] Write a small script (for example, a disk usage report)
- [ ] Turn the script into a systemd service
- [ ] Add a systemd timer for it
- [ ] Verify with `systemctl status` and `systemctl list-timers`

## Phase 3: Storage and backups
- [ ] Partition the second disk with `fdisk`
- [ ] Format it (ext4) and mount it at `/mnt/backup`
- [ ] Add it to `/etc/fstab` (use the UUID from `blkid`)
- [ ] Reboot and confirm it mounts automatically
- [ ] Create a swap file with `mkswap` and `swapon`
- [ ] Write `backup.sh` using `rsync` over SSH
- [ ] Schedule it with cron
- [ ] Test a restore from the backup
- [ ] Install `nfs-kernel-server` and share a folder (intentionally with `no_root_squash` for testing later)
- [ ] Mount the NFS share from Kali

## Phase 4: Containers
- [ ] Install Docker on the VM
- [ ] Write a Dockerfile for the web app
- [ ] Build the image and run it with port mapping
- [ ] Add a volume for persistent data
- [ ] Limit CPU and memory for the container
- [ ] Check logs with `docker logs`
- [ ] Stop and remove the container, confirm the data survives

## Phase 5: Firewall and access control
- [ ] List current rules: `iptables -L -n -v` (or `ufw status`)
- [ ] Allow SSH first (do this before any default deny)
- [ ] Allow HTTP
- [ ] Set the default policy to deny incoming
- [ ] Block one test IP, then allow it again
- [ ] Make the rules persistent after reboot
- [ ] Check the status of AppArmor: `aa-status`
- [ ] Optional: put one service under a custom AppArmor profile
- [ ] Take snapshot: `hardened`

## Phase 6: Logging and monitoring
- [ ] Read `/var/log/auth.log` and `/var/log/syslog`
- [ ] Use `grep "Failed password"` to find failed logins
- [ ] Write a script that counts failed logins per IP (`grep`, `awk`, `sort`, `uniq -c`)
- [ ] Add sudo usage to the report
- [ ] Schedule the script daily with cron
- [ ] Save the report to a file and check the output

## Phase 7: Attack it
**Round 1: baseline (restore `baseline-before-hardening`)**
- [ ] Scan with `nmap` (ports, services, versions)
- [ ] Try SSH brute force against a test account
- [ ] Look for SUID binaries: `find / -perm -4000 2>/dev/null`
- [ ] Check the NFS share: `showmount -e`, mount it, test `no_root_squash`
- [ ] Look for world-writable files and weak cron jobs
- [ ] Review what the attacks left in the logs
- [ ] Write down every finding

**Round 2: hardened (restore `hardened`)**
- [ ] Repeat the same scans and attacks
- [ ] Compare the results with round 1
- [ ] Check whether fail2ban and the firewall reacted

## Phase 8: Audit and report
- [ ] Install and run Lynis: `lynis audit system`
- [ ] Fix the top warnings
- [ ] Change the NFS share to `root_squash`
- [ ] Remove unneeded services and unwanted SUID binaries
- [ ] Re-run Lynis and note the score improvement
- [ ] Write the findings report (what I built, found, and fixed)

## Final deliverables
- [ ] `setup.sh` that rebuilds the hardened server
- [ ] README with a step-by-step guide per phase
- [ ] Before and after attack comparison
- [ ] Final report
- [ ] Push everything to GitHub

## Bonus: cloud
- [ ] Create a cloud VM (AWS EC2 free tier)
- [ ] Recreate the hardened setup there
- [ ] Compare iptables with a security group
- [ ] Compare local users with IAM
- [ ] Add the comparison to the report