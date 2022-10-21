## Commands
### Linux
#### iproute2
```
ip route list
ip route show table all
ip link set enp3s0 up
ip route flush table main
ip rule add to 192.168.20.4/32 enp3s0
ip route add from 192.168.20.4/32 table enp3s0
ip route add default via 192.168.20.4 dev enp3s0 table enp3s0

```
#### nmcli
```
nmcli device show
```
#### Recommended Practices for Configuring Host Networks
Always use the oVirt Engine to modify the network configuration of hosts in your clusters. Otherwise, you might create an unsupported configuration. For details, see [Network Manager Stateful Configuration (nmstate)](https://ovirt.org/documentation/administration_guide/index#con-Network-Manager-Stateful-Configuration-nmstate).

If your network environment is complex, you may need to configure a host network manually before adding the host to the oVirt Engine.

Consider the following practices for configuring a host network:

-   Configure the network with Cockpit. Alternatively, you can use `nmtui` or `nmcli`.
    
-   If a network is not required for a self-hosted engine deployment or for adding a host to the Engine, configure the network in the Administration Portal after adding the host to the Engine. See [Creating a New Logical Network in a Data Center or Cluster](https://ovirt.org/documentation/administration_guide/index#Creating_a_new_logical_network_in_a_data_center_or_cluster).
    
-   Use the following naming conventions:
    
    -   VLAN devices: `_VLAN_NAME_TYPE_RAW_PLUS_VID_NO_PAD_`
        
    -   VLAN interfaces: `_physical_device_._VLAN_ID_` (for example, `eth0.23`, `eth1.128`, `enp3s0.50`)
        
    -   Bond interfaces: `bond_number_` (for example, `bond0`, `bond1`)
        
    -   VLANs on bond interfaces: `bond_number_._VLAN_ID_` (for example, `bond0.50`, `bond1.128`)
        
    
-   Use [network bonding](https://ovirt.org/documentation/administration_guide/index#sect-Network_Bonding). Network teaming is not supported in oVirt and will cause errors if the host is used to deploy a self-hosted engine or added to the Engine.
    
-   Use recommended bonding modes:
    
    -   If the `ovirtmgmt` network is not used by virtual machines, the network may use any supported bonding mode.
        
    -   If the `ovirtmgmt` network is used by virtual machines, see [_Which bonding modes work when used with a bridge that virtual machine guests or containers connect to?_](https://access.redhat.com/solutions/67546).
        
    -   oVirt’s default bonding mode is `(Mode 4) Dynamic Link Aggregation`. If your switch does not support Link Aggregation Control Protocol (LACP), use `(Mode 1) Active-Backup`. See [Bonding Modes](https://ovirt.org/documentation/administration_guide/index#Bonding_Modes) for details.
        
    
-   Configure a VLAN on a physical NIC as in the following example (although `nmcli` is used, you can use any tool):
    
    ```
    # nmcli connection add type vlan con-name vlan20 ifname enp3s0.20 dev enp3s0 id 20
    ```
    
`nmcli con mod vlan20 +ipv4.dns 8.8.8.8 +ipv4.addresses 192.168.20.4 +ipv4.gateway 192.168.20.254`
    
-   Configure a VLAN on a bond as in the following example (although `nmcli` is used, you can use any tool):
    
    ``` bash
    nmcli connection add type bond con-name bond0 ifname bond0 bond.options "mode=active-backup,miimon=100" ipv4.method disabled ipv6.method ignore
    nmcli connection add type ethernet con-name eth0 ifname eth0 master bond0 slave-type bond
    nmcli connection add type ethernet con-name eth1 ifname eth1 master bond0 slave-type bond
    nmcli connection add type vlan con-name vlan50 ifname bond0.50 dev bond0 id 50
    
	nmcli con mod vlan50 +ipv4.dns 8.8.8.8 +ipv4.addresses 123.123.0.1/24 +ipv4.gateway 123.123.0.254
    ```
    
-   Do not disable `firewalld`.
    
-   Customize the firewall rules in the Administration Portal after adding the host to the Engine. See [Configuring Host Firewall Rules](https://ovirt.org/documentation/administration_guide/index#Configuring_Host_Firewall_Rules).

#### Disable interface filter
``` bash
sudo sysctl -w net.ipv4.conf.all.rp_filter=2
```

### Windows
#### Enable Remote Desktop using PowerShell on Windows 10

To enable the remote desktop protocol with PowerShell, use these steps:

1.  Open **Start** on Windows 10.``
    
2.  Search for **PowerShell**, right-click the top result, and select the **Run as administrator** option.
    
3.  Type the following command to enable the remote desktop protocol and press **Enter**:

```powershell
    Set-ItemProperty -Path 'HKLM:\\System\\CurrentControlSet\\Control\\Terminal Server' -name "fDenyTSConnections" -value 0

	```
	
 ![PowerShell enable Remote Desktop](https://i0.wp.com/pureinfotech.com/wp-content/uploads/2020/02/enable-remote-desktop-powershell-windows-10-2022.webp?resize=827%2C158&quality=78&strip=all&ssl=1)

Enable remote desktop through the Windows Firewall and press **Enter**: 
```powershell
 Enable-NetFirewallRule -DisplayGroup "Remote Desktop"
```

#### Disable Remote Desktop using PowerShell

To disable the remote desktop protocol with PowerShell, use these steps:
```powershell
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -value 1
```

![PowerShell disable Remote Desktop](https://i0.wp.com/pureinfotech.com/wp-content/uploads/2020/02/disable-remote-desktop-powershell-windows-10-2022.webp?resize=827%2C158&quality=78&strip=all&ssl=1)
```powershell
    Disable-NetFirewallRule -DisplayGroup "Remote Desktop"
```
