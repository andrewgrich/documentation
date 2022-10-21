## Bookmarks
### [Proxmox Dowload](https://www.proxmox.com/en/downloads/category/iso-images-pve)
### [nmcli cheatsheet](https://www.golinuxcloud.com/nmcli-command-examples-cheatsheet-centos-rhel/#27_Delete_connection) 
### [oVirt vlan tip](https://ovirt.org/documentation/administration_guide/index.html#Configuring_Host_Firewall_Rules)
```
nmcli connection add type vlan con-name vlan50 ifname eth0.50 dev eth0 id 50
nmcli con mod vlan50 +ipv4.dns 8.8.8.8 +ipv4.addresses 123.123.0.1/24 +ipv4.gateway 123.123.0.254
```

