---
tags:
  - phase1
date: 2026-11-21
Links: "[[2026-11-21]]"
---
## Why do we need a static IP address :
Because of [[DHCP]] lease :
- the IP address that given by dhcp isn't permanent and it can be changed after , so we need to make it static to guarantee the SSH connection .
## Keeping SSH alive during the change :
- the configuration files in the machine are :
00-installer-config.yaml : only describe the enp0s3 interface 
  ```shell
sysadmin@sysadmin-VirtualBox:/etc/netplan$ sudo cat 00-installer-config.yaml 
#This is the network config written by 'subiquity'
network:
  ethernets:
    enp0s3:
      dhcp4: true
      dhcp6: true
      match:
        macaddress: 08:00:27:5d:49:1d
      set-name: enp0s3
  version: 2
  ```

  for the rest :

  ```shell
  sysadmin@sysadmin-VirtualBox:/etc/netplan$ sudo cat 01-network-manager-all.yaml 
#Let NetworkManager manage all devices on this system                      
network:                                                                    
  version: 2                                                                
  renderer: NetworkManager      
  ```
which hands all network control to **NetworkManager**. This is typical of Ubuntu Desktop.
- So `enp0s8` got `.101` because NetworkManager automatically created a DHCP profile for it.
- We are going to use [[nmcli]] NetworkManager's command-line client.

## Steps :

### Step 0: find the connection name 
- I used 2 commands :
```bash 
nmcli device status
nmcli connection show
```

![[interface-name.png]]

- the name of the host-only connection is **Wired connection 1 
### Step 1: add the static IP next to DHCP
```bash
sudo nmcli connection modify "Wired connection 1" ipv4.addresses 192.168.56.10/24
sudo nmcli connection up "Wired connection 1"
```
- The first command use modify to edit the saved profile , the static IP added to enp0s8 interface , so it dosn't block the ssh session.
- The second command re-applied the profile .
Now when we did the commands enp0s8 have 2 IP addresses :
- 192.168.56.10/24 
- 192.168.56.101/24 
### Step 2: test from the host
In the host terminal :
```bash 
ssh sysadmin@192.168.56.10
```
- The connection succeed :
		![[ssh-new-ip.png]]

### Step 3: remove DHCP from that card
```bash
sudo nmcli connection modify "Wired connection 1" ipv4.method manual ipv4.never-default yes
sudo nmcli connection up "Wired connection 1"
```
- `ipv4.method manual` turn **DHCP** off , and `ipv4.never-default yes` guarantees that this card can never become the default  route .
-  enp0s8 have only the static IP : 192.168.56.10/24
```bash
nmcli -f ipv4.method,ipv4.addresses connection show "Wired connection 1"
```
- output :
```bash
ipv4.method:                            manual
ipv4.addresses:                         192.168.56.10/24
```
### Step 4: update the host shortcut
```nano
Host lab
    HostName 192.168.56.10
    User sysadmin
```


