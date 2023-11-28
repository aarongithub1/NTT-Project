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

## Install Active Directory

### Promote server to Domain Controller

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

- Select the option to "Add a new forest"
- Type the domain name that you want "widgets.localdomain" - click Next

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

## Create User and Admin Accounts:

Use this format for the accounts:
example user: Adam West
user account: u-awest
admin account: a-awest

![DC_AD_add user_1](https://github.com/aarongithub1/NTT-Project/assets/31551830/9847af5f-bdea-4d07-b332-9083c4433cdd)

![DC_AD_new user](https://github.com/aarongithub1/NTT-Project/assets/31551830/1e97f3da-766f-4588-963b-761103f685cb)

![DC_AD_user_AdamWest](https://github.com/aarongithub1/NTT-Project/assets/31551830/f0d0b3ec-e991-4eab-99d7-c95925ef02a7)

![DC_AD_user password](https://github.com/aarongithub1/NTT-Project/assets/31551830/9ccac8ca-ad75-4d13-92fa-f1f81489171c)

![DC_AD_user_complete](https://github.com/aarongithub1/NTT-Project/assets/31551830/407c35b6-38bc-48a7-a02c-8f6073673269)

![DC_AD_AdamWest_domain admins](https://github.com/aarongithub1/NTT-Project/assets/31551830/6125d865-a01e-44f4-a04e-0ea3b2d68261)

After Creating User and Admin accounts, add the "admin" accounts to the “domain admins” security group and click the "Check Names" Button. - Then click OK

![DC_AD_AdamWest_domain admins_2](https://github.com/aarongithub1/NTT-Project/assets/31551830/12f9121b-dfed-472a-aae6-e55d053f5c1f)

## Prepare Win10 to join the domain

- Change the hostname to "win10" - then reboot.
- Change the primary DNS server to the IP address of the DC Server
- Set NTP to sync with "dc.widgets.localdomain"

![Screenshot 2023-11-15 at 4 14 07 PM](https://github.com/aarongithub1/NTT-Project/assets/31551830/725e79b7-05de-4eb0-945a-7a7f6561df9e)

![Screenshot 2023-11-15 at 4 14 38 PM](https://github.com/aarongithub1/NTT-Project/assets/31551830/0f293b5b-ec4e-4088-8926-5cae7c337f9a)

![Screenshot 2023-11-15 at 4 15 19 PM](https://github.com/aarongithub1/NTT-Project/assets/31551830/1b0eb868-209d-451c-bbe3-3ab0493b7cc6)

![Screenshot 2023-11-15 at 4 33 40 PM](https://github.com/aarongithub1/NTT-Project/assets/31551830/d13512b0-e4f6-4704-afa5-9db708dff19f)

## Join Win10 to the widgets.localdomain domain

Join win10 to the domain with your domain admin account, then login with your domain user account

![Screenshot 2023-11-15 at 4 38 03 PM](https://github.com/aarongithub1/NTT-Project/assets/31551830/e2f1c136-739f-4002-bb9c-4b36b7191362)

![Screenshot 2023-11-15 at 4 38 59 PM](https://github.com/aarongithub1/NTT-Project/assets/31551830/5a70158e-bb35-4f3a-b98f-d3e35b235405)

![Screenshot 2023-11-15 at 4 42 28 PM](https://github.com/aarongithub1/NTT-Project/assets/31551830/036227b9-6be5-4d12-9224-fe86ba664db6)

![Screenshot 2023-11-15 at 4 46 24 PM](https://github.com/aarongithub1/NTT-Project/assets/31551830/15af2bf8-2251-4e02-a67a-bb1fff8ec17c)

## Set the desktop background with GPO (Group Policy Object)

- login to the DC Server and open the Group Policy Management Console
- Edit the Default Domain Policy

Set the desktop image to:
```
c:\windows\web\wallpaper\windows\img0.jpg
```

![Screenshot 2023-11-15 at 4 51 11 PM](https://github.com/aarongithub1/NTT-Project/assets/31551830/6577596c-5e08-479c-84c1-4dc4c0f4b574)

![Screenshot 2023-11-15 at 4 51 44 PM](https://github.com/aarongithub1/NTT-Project/assets/31551830/89c8040c-7e0a-4ab4-be37-a89755889bad)

- Logout of win10
- log back in
- The background should be updated

---

# Stage 3


   
### Add new devices to the lab workspace, and link them up

![IIS_Topology](https://github.com/aarongithub1/NTT-Project/assets/31551830/e56ce06a-48d4-4cb4-ac53-f977769200e2)

### Prepare a Win2012r2 server to join the domain
```
hostname: iis
```
```
join to the widgets domain "widgets.localdomain"
```
```
ip address: 10.128.0.80
subnet mask: 255.255.255.0
default gateway: 10.128.0.1
DNS1: 10.128.0.10
DNS2: 10.128.0.1
```
```
NTP sync with: dc.widgets.localdomain
```

![IIS_Host_name](https://github.com/aarongithub1/NTT-Project/assets/31551830/55c51582-762e-4522-9e7c-ce339d05508d)

![IIS_Static_IP](https://github.com/aarongithub1/NTT-Project/assets/31551830/a92932ed-9c04-4b66-a175-39f6653c4310)

![IIS_NTP_sync](https://github.com/aarongithub1/NTT-Project/assets/31551830/50366cb9-f65c-4883-ad3c-88c1fea1fc5f)

## Install the “Internet Information Services” server role

- Open Server Manager
- Click the Manage menu, and then click Add Roles and Features
- Before You Begin page, click Next
- Installation Type page, select Role-based or feature-based installation to configure a single server. Click Next

![Screenshot 2023-11-15 at 5 23 35 PM](https://github.com/aarongithub1/NTT-Project/assets/31551830/bfdf3b60-c01a-41e5-828d-d7d39abb6f71)

- On the Server Selection page, select Select a server from the server pool, and then select a server - Click Next

![Screenshot 2023-11-15 at 5 25 42 PM](https://github.com/aarongithub1/NTT-Project/assets/31551830/55d9fc9b-d2e6-4cd1-9a3c-a922e4eded4f)

- On the Server Roles page, select Web Server (IIS)

![Screenshot 2023-11-15 at 5 27 06 PM](https://github.com/aarongithub1/NTT-Project/assets/31551830/ff5832eb-ccb8-4f94-b8ca-d2f018831e8b)

- On the Add Roles and Features wizard, click Add Features - then on the Server Roles page, click Next

![Screenshot 2023-11-15 at 5 28 10 PM](https://github.com/aarongithub1/NTT-Project/assets/31551830/4bf1e825-4d70-4aec-8468-ba482f3244eb)

![Screenshot 2023-11-15 at 5 29 38 PM](https://github.com/aarongithub1/NTT-Project/assets/31551830/2ca026f0-5315-44f4-b1cf-1c6a314bbfb0)

- On the Web Server Role (IIS) page, click Next

- On the Role Services page, click Next

![Screenshot 2023-11-15 at 5 30 53 PM](https://github.com/aarongithub1/NTT-Project/assets/31551830/254bc343-efa8-43de-b639-bcf7fb11a195)

- On the Confirmation page, select "Restart the destination server automatically if required"
- On the Confirmation page, click Install

![Screenshot 2023-11-15 at 5 34 20 PM](https://github.com/aarongithub1/NTT-Project/assets/31551830/f59d43d0-cae0-4f13-a449-cd256d49c6e5)

![IIS_add_roles_features](https://github.com/aarongithub1/NTT-Project/assets/31551830/d47de7ee-269f-4dcd-b529-276d6d41ce1a)

![IIS_roles_complete](https://github.com/aarongithub1/NTT-Project/assets/31551830/2e82752a-9c9c-438e-ae9f-f6e3d7db9cd6)

![IIS_add_localhost_trusted](https://github.com/aarongithub1/NTT-Project/assets/31551830/fea2db64-567f-436d-a5a3-871fe8bc686f)

## Add a test webpage, and verify access over the LAN network
### Setup a test webpage on IIS

- Open Notepad and type the following HTML code:
```
<html>
<head>
<title> IIS test validation website</title>
</head>
<body>
<p>test website validation completed</p>
</body>
</html>
```
- Save the file as "test.html" and place it in the following folder:
```
c:\inetpub\wwwroot\test.html
```

![Test_Page_Save](https://github.com/aarongithub1/NTT-Project/assets/31551830/6bc3b18d-e8dd-47d6-b1d0-2a1bb2cce988)


- Test the page in the local browser on IIS.
```
http://localhost/test.html
```

![Test_Page_on_IIS_Server](https://github.com/aarongithub1/NTT-Project/assets/31551830/20d5adac-c4e6-49e4-bb8f-471de0f2c649)


- Also test your page from the win10 workstation
```
http://iis.widgets.localdomain/test.html
```

![Test_Page_on_WIN10](https://github.com/aarongithub1/NTT-Project/assets/31551830/e95627cd-7b8a-409b-8111-829f17ea4f51)


## Setup a LAMP server on an Ubutu Machine

### Add new devices to the lab workspace, and link them up

![Screenshot 2023-11-16 at 9 25 27 AM](https://github.com/aarongithub1/NTT-Project/assets/31551830/ad785e2d-5dbc-48b7-aa0a-16aa78ecbc51)

### Prepare the Ubuntu server
- Configure the network settings

```
ip = 10.128.10.80
netmask = 255.255.255.0
gateway = 10.128.10.1

dns = 10.128.0.10,10.128.10.1
```

![Screenshot 2023-11-16 at 9 27 55 AM](https://github.com/aarongithub1/NTT-Project/assets/31551830/aa6e36ac-e001-442b-81ce-90dd1cafeac6)

![Screenshot 2023-11-16 at 9 30 55 AM](https://github.com/aarongithub1/NTT-Project/assets/31551830/015f3b6c-10a7-4540-b3e1-56aa835a1115)

### Update the hosts file
- Open Terminal and change to root
```
sudo -i
```

- Edit the hosts file
```
nano /etc/hosts
```

```
127.0.0.1 localhost.localdomain localhost
10.128.10.80 www.widgets.localdomain www
```

![Screenshot 2023-11-16 at 9 33 39 AM](https://github.com/aarongithub1/NTT-Project/assets/31551830/87b0ee7c-6ac5-40b0-8498-4b13264a1b23)

![Screenshot 2023-11-16 at 10 25 59 AM](https://github.com/aarongithub1/NTT-Project/assets/31551830/54c6525f-bcb7-4d53-a303-72b3c06b6588)

### Set hostname with hostnamectl
```
hostnamectl set-hostname www
hostnamectl
```

### Update the packages on the server
```
apt update -y
apt upgrade -y
apt dist-upgrade -y
apt autoremove -y
apt autoclean -y
systemctl reboot
```

### On the Ubuntu Server, open the Terminal. Then Switch to root.

<img width="681" alt="LAMP_switch to Root" src="https://github.com/aarongithub1/NTT-Project/assets/31551830/34a86796-5aaa-48cd-9173-c829cb085196">

### Install DokuWiki
```
apt install php php-gd php-xml php-json -y
systemctl enable --now apache2
ufw allow Apache
wget https://download.dokuwiki.org/src/dokuwiki/dokuwiki-stable.tgz
mkdir /var/www/html/dokuwiki
tar xzf dokuwiki-stable.tgz -C /var/www/html/dokuwiki/ --strip-components=1
```

create the config file for the wiki
```
nano /etc/apache2/sites-available/dokuwiki.conf
```
Add the following, keep the tabbed spacing.
```
<VirtualHost *:80>
          ServerName    www.widgets.localdomain
          DocumentRoot  /var/www/html/dokuwiki
  
          <Directory ~ "/var/www/html/dokuwiki/(bin/|conf/|data/|inc/)">
              <IfModule mod_authz_core.c>
                  AllowOverride All
                  Require all denied
              </IfModule>
              <IfModule !mod_authz_core.c>
                  Order allow,deny
                  Deny from all
              </IfModule>
          </Directory>
  
          ErrorLog   /var/log/apache2/dokuwiki_error.log
          CustomLog  /var/log/apache2/dokuwiki_access.log combined
</VirtualHost>
```
