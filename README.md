# Network Tools and Technologies Project

### This is a project where I built a small network

# Stage 1 (Network Setup)

## In GNS3 - Drag network equipment into the project area and connect all the devices
- FortiGate (Firewall)
- LAN Switch
- DMZ Switch
- Win10 workstation

![Screenshot 2023-11-14 at 12 32 27 PM](https://github.com/aarongithub1/NTT-Project/assets/31551830/4ee46fdc-9c6f-4364-b831-ec20d32d3f23)

## Configure the FortiGate Firewall

### Create password:

![Screenshot 2023-11-14 at 12 39 18 PM](https://github.com/aarongithub1/NTT-Project/assets/31551830/f5cd74fa-65de-49c5-a67b-c4e8c0c03e83)

## Configure Interface

```
conf sys int
      edit port2
          set allowaccess ping http https ssh
          set ip 10.128.0.1/24
      end
```

![Screenshot 2023-11-14 at 12 46 15 PM](https://github.com/aarongithub1/NTT-Project/assets/31551830/e29a246d-b887-4f11-97c9-32c60729b11d)

## Configure the DHCP Server for LAN interface

```
conf sys dhcp server
      edit 1
          set default-gateway 10.128.0.1
          set netmask 255.255.255.0
          set interface port2
          config ip-range
              edit 1
                  set start-ip 10.128.0.100
                  set end-ip 10.128.0.199
              next
          end
      next
  end
```

![Screenshot 2023-11-14 at 12 49 47 PM](https://github.com/aarongithub1/NTT-Project/assets/31551830/f1165e3d-5e8a-4391-ad88-420f16fe0f84)

## Add Windows workstation (Test DHCP Server / Test Connectivity)

### Test DHCP address assignment:

```
ipconfig /all
```

![Windows_ipconfig](https://github.com/aarongithub1/NTT-Project/assets/31551830/84469464-3eca-4c41-9a54-d5fa24ca844d)

### Test Connectivity:

Ping:
- LAN - 10.128.0.1
- WAN - 8.8.8.8
- DNS - google.com

![Windows_ping](https://github.com/aarongithub1/NTT-Project/assets/31551830/4bcf179e-d944-4753-aa18-7d9b7a6ada44)

## Connect to the Firewall GUI

### Open a web browser and type the gateway address into the URL box

![FortiGate_GUI](https://github.com/aarongithub1/NTT-Project/assets/31551830/32c0c0aa-3bdb-4169-a822-6ce868fa050f)

### Make System changes

```
Go to System > Settings
```

```
hostname = firewall
  timezone = GMT -6:00 Central Time (US & Canada)
  setup device as local NTP server = enabled
      list on interfaces = port2, port4
  idle timeout = 60
  auto file system check = enabled
```
![FortiGate_Sys_Settings](https://github.com/aarongithub1/NTT-Project/assets/31551830/d11a1870-2531-4c8f-bb6e-d0bdf8a7bc20)

### Backup Firewall settings
- Click Save

## Complete Network setup on FortiGate Firewall

### Instructions

- Connect to the GUI
- Backup the firewall Config
- Configure network interfaces
      - port1 WAN
      - port2 LAN
      - port3 GUEST
      - port4 DMZ
- Enable DNS
- Configure the firewall system DNS
- Configure Network DNS
      - LAN DNS
      - GUEST DNS
      - DMZ DNS
- Create Service Objects
      - LAN services
      - DMZ services
- Configure firewall Rules
      - LAN-to-WAN policy
      - DMZ-to-WAN policy
      - LAN-to-DMZ policy
      - DMZ-to-LAN policy
      - WAN-to-DMZ policy
- Backup the firewall Config

### Connect to GUI
```
http://10.128.0.1/
```

### Backup Firewall

### Configure Network Interfaces
```
10.128.0.0/24 as the LAN network
10.128.99.0/24 as the GUEST network
10.128.10.0/24 as the DMZ network
```

### Edit Network interfaces:
```
Edit the network interfaces under: (Network > Interfaces)
```
port1 WAN
```
edit port1
    alias = WAN
    role = WAN
Click "OK" to Apply the changes
```

port2 LAN
```
edit port2
    alias = LAN
    role = LAN
    create address object matching subnet = enabled
    administrative access = https, http, ping, ssh
    dns server = same as interface IP
    expand advanced
        ntp server = local
Click "OK" to Apply the changes
```

port3 GUEST
```
edit port3
    alias = GUEST
    role = LAN
    ip/network mask = 10.128.99.1/24
    create address object matching subnet = enabled
    administrative access = ping, ssh
    dhcp server = enabled
    dns server = same as interface IP
    expand advanced
        ntp server = local
Click "OK" to Apply the changes
```

port4 DMZ
```
edit port4
    alias = DMZ
    role = DMZ
    ip/network mask = 10.128.10.1/24
    create address object matching subnet = enabled
    administrative access = ping
Click "OK" to Apply the changes
```

### Enable DNS
```
Enable DNS under: (System > Feature Visibility)
```

```
dns database = enabled
Click "OK" to Apply the changes
```

### Configure the firewall system DNS
```
Configure DNS settings under: (Network > DNS)
```

```
dns servers = specify
  primary dns server = 8.8.8.8
  secondary dns server = 1.1.1.1
Click "OK" to Apply the changes
```

### Configure Network DNS:
LAN DNS
```
Configure Network DNS settings under: (Network > DNS Servers)
```

```
Create New
  interface = LAN(port2)
  mode = recursive
Click "OK" to Apply the changes
```

GUEST DNS
```
Create New
  interface = GUEST(port3)
  mode = recursive
Click "OK" to Apply the changes
```

DMZ DNS
```
Create New
  interface = DMZ(port4)
  mode = recursive
Click "OK" to Apply the changes
```

### Create Service Objects
```
Configure Service Objects under: (Policy & Objects > Services)
```
LAN services
```
Create New > Service Group
  name = LAN-services-group
  members = ALL_ICMP, NTP, RDP, SSH, Web Access, Windows AD
```

DMZ services
```
  Create New > Service Group
  name = DMZ-services-group
  members = ALL_ICMP, FTP, RDP, SSH, Web Access
```

Configure firewall Rules
```
Configure firewall rules under: (Policy & Objects > IPv4 Policy)
```

LAN-to-WAN policy
```
Create New
  name = LAN-to-WAN
  incoming interface = LAN
  outgoing interface = WAN
  source = port2 address
  destination = all
  service = all
  NAT = enabled
  Click "OK" to Apply the changes
```

DMZ-to-WAN policy
```
  Create New
  name = DMZ-to-WAN
  incoming interface = DMZ
  outgoing interface = WAN
  source = port4 address
  destination = all
  service = all
  NAT = enabled
  Click "OK" to Apply the changes
```

LAN-to-DMZ policy
```
  Create New
  name = LAN-to-DMZ
  incoming interface = LAN
  outgoing interface = DMZ
  source = port2 address
  destination = port4 address
  service = DMZ-services-group
  NAT = disabled
  Click "OK" to Apply the changes
```

DMZ-to-LAN policy
```
  Create New
  name = DMZ-to-LAN
  incoming interface = DMZ
  outgoing interface = LAN
  source = port4 address
  destination = port2 address
  service = LAN-services-group
  NAT = disabled
  Click "OK" to Apply the changes
```

WAN-to-DMZ policy
```
  Create New
  name = WAN-to-DMZ
  incoming interface = WAN
  outgoing interface = DMZ
  source = all
  destination = port4 address
  service = DMZ-services-group
  NAT = disabled
  Click "OK" to Apply the changes
```

### Backup the firewall Config


---


# Stage 2 (Domain Setup)

## Prepare a Win2012r2 server

### Drage the Windows Server 2012r2 into the GNS3 project area and add a link to the LAN Switch

![Add_Windows Server_2012r2](https://github.com/aarongithub1/NTT-Project/assets/31551830/31ad74b8-cd05-44bd-943e-172b5aaedc8d)

### Start the Win2012r2 server in GNS3 then double click to open in VNC
- select keyboard layout
- Click NEXT then I accept on the license agreement
- Create a password
- Use the “Send Crtl+Alt+Del” macro to login
- Type in your password

### Set a static IP address:
```
Change Adapter Settings > Ethernet Instance > Properties > TCP/IPv4 > Properties
```
```
  ip address: 10.128.0.10
  subnet mask: 255.255.255.0
  default gateway: 10.128.0.1
  
  DNS1: 127.0.0.1
  DNS2: 10.128.0.1
```

![Win_Server_Static_IP](https://github.com/aarongithub1/NTT-Project/assets/31551830/c9b9df2d-1661-4028-b0d6-9ddb27aedfb8)

### Test network connectivity:

Ping remote destinations to test LAN, WAN, and DNS network connectivity
```
  LAN - 10.128.0.1
  WAN - 8.8.8.8
  DNS - google.com
```

![Ping_Connectivity](https://github.com/aarongithub1/NTT-Project/assets/31551830/06c05fe0-ce6c-43ab-811a-39a32cde41b2)

### Configure NTP:
Windows Time service tools and settings
Set timezone and sync with the LAN interface IP on the firewall.
```
10.128.0.1
```

![Time Zone](https://github.com/aarongithub1/NTT-Project/assets/31551830/d1a018f8-36a7-41f7-a828-c28664739b28)

![Internet Time](https://github.com/aarongithub1/NTT-Project/assets/31551830/ba1cd29a-c192-4945-a5bd-67e3c31a9705)

![Time Sync](https://github.com/aarongithub1/NTT-Project/assets/31551830/4cbfe8f5-2f7a-4df3-8f23-949767e30b5d)

### Change the hostname:
Change the hostname to “dc” - (short for domain controller)

### Install Active Directory

Promote server to Domain Controller

- Click Manage > Add Roles and Features
  
Once in the Wizard:
- Click Next on the first screen
- Keep the defaults (Role-based or feature-based installation) on Installation type and Click Next
- Keep the defaults (Select a server from the server pool), make sure your new server is highlighted, Click Next
- Put a check mark next to "Active Directory Domain Services" on Server Roles, Click Add Features on the popup screen, Click Next
- On the features screen, Click Next
- On AD DS screen, Click Next
- And finally on the confirmation screen, Click Install
- Active Directory binaries will be installed
- Once that finishes Click the Close Button

At the top of the screen there should be a flag next to the word manage with a yellow caution symbol.

![DC_Feature_Install_yellow_Flag](https://github.com/aarongithub1/NTT-Project/assets/31551830/2a0ca2c6-81de-4fc4-bd22-f2233742ee06)

- Click the flag, a window will open - stating "Configuration Required for Active Directory Domain Services."
- Click the "Promote this server to a domain controller" link

![DC_promote server](https://github.com/aarongithub1/NTT-Project/assets/31551830/7119b322-f4ea-412a-916f-362318fb4473)

- Select the option to "Add a new forest" and then type the domain name that you want, then click Next  

![DC_add new forest](https://github.com/aarongithub1/NTT-Project/assets/31551830/ef3ed1c9-46d6-43c5-9cb4-a90e30dcd9b3)

On the Domain Controller Options screen:
- Select the Forest functional and Domain functional level
- In "Specify domain controller capabilities" leave defaults
- Enter the Dictionary Services Restore Mode (DSRM) password - Click Next
- DNS options screen, ignore the warning about the delegation - Click Next
- Additional options page, make sure that the name of your domain is listed as the NetBios domain name. For my Domain of widgets.localdomain the NetBios name is widgets – Click Next
- Paths screen, use defaults - click Next
- Review options - click Next
- Review and Click Install
- Once the install completes the server will reboot
- Once rebooted you will sign into your new domain

