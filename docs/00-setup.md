---
tags:
  - homelab
  - linux
  - htb
date: 2026-11-11
status: done
Links:
---

## Objective
```
- Install and setup ubuntu in the virtualbox
- Add a host-only adapter and a second disk
- Open an SSH connection
```



Host-only adapter and second disk added
## Create a host-only network : 
![](host_only.png)
## the NAT adapter to connecting to the internet 
![](NAT.png)
## add the host-only adapter 
![](host_only1.png)

## Add second disk 
![](add_second_disk.png)

## Verification
- Inside the VM: 
	``` ip -4 a ``` 
	Shows `enp0s3` = 10.0.2.15 (NAT) and `enp0s8` = 192.168.56.101 (host-only).
	![](ip_a_local_host.png)
- From the host:
	 ```bash
		 ssh sysadmin@192.168.56.101
	 ```
	![](ssh_shell.png)
	![](ssh_run.png)
## Add a shortcut to connect to the lab :
```bash
nano ~/.ssh/config
```
open (creates)  personal SSH settings file :
```nano
Host lab
	HostName 192.168.65.101
	User sysadmin
```
now connecting just using the command :
```bash
 ssh lab
```
