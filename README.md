# NTT-Project
This is a project where I built a small network

## In GNS3 - drag all network equipment into the project and connect all the devices
- FortiGate (Firewall)
- LAN Switch
- DMZ Switch
- Win10 workstation

![Screenshot 2023-11-14 at 12 32 27 PM](https://github.com/aarongithub1/NTT-Project/assets/31551830/4ee46fdc-9c6f-4364-b831-ec20d32d3f23)

## Configure the FortiGate Firewall
- Create password

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

Test DHCP address assignment:

'''
ipconfig /all
'''

![Windows_ipconfig](https://github.com/aarongithub1/NTT-Project/assets/31551830/84469464-3eca-4c41-9a54-d5fa24ca844d)

Test Connectivity:

Ping:
- LAN - 10.128.0.1
- WAN - 8.8.8.8
- DNS - google.com

![Windows_ping](https://github.com/aarongithub1/NTT-Project/assets/31551830/4bcf179e-d944-4753-aa18-7d9b7a6ada44)

