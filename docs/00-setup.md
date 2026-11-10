## Goal of phase 00 :
```
- Install and setup ubuntu in the virtualbox
- Add a host-only adapter and a second disk
- setting the ho
```



Host-only adapter and second disk added
## Create a host-only network : 
![](assets/host_only.png)
## the NAT adapter to connecting to the internet 
![](assets/NAT.png)
## add the host-only adapter 
![](assets/host_only1.png)

## Add second disk 
![](assets/add_second_disk.png)

## Verification
- Inside the VM: 
	``` ip -4 a ``` 
	Shows `enp0s3` = 10.0.2.15 (NAT) and `enp0s8` = 192.168.56.101 (host-only).
	![](assets/ip_a_local_host.png)
- From the host:
	 ```bash
		 ssh sysadmin@192.168.56.101
	 ```
	![](assets/ssh_shell.png)
	![](assets/ssh_run.png)
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
