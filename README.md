# NTT-Project
This is a project where I built a small network

## In GNS3 - drag network equipment into the project and connect all the devices
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

Edit Network interfaces:
```
Edit the network interfaces under: **Network > Interfaces**
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

Enable DNS
```
Enable DNS under: **System > Feature Visibility**
```

```
dns database = enabled
Click "OK" to Apply the changes
```

Configure the firewall system DNS
```
Configure DNS settings under: **Network > DNS**
```
