# NTT-Project
This is a project where I built a small network

## Step 1
In GNS3 - drag all network equipment into the project and connect all the devices
- FortiGate (Firewall)
- LAN Switch
- DMZ Switch
- Win10 workstation

![Screenshot 2023-11-14 at 12 32 27 PM](https://github.com/aarongithub1/NTT-Project/assets/31551830/4ee46fdc-9c6f-4364-b831-ec20d32d3f23)

## Step 2
Configure the FortiGate Firewall
- Create password
- Configure Interface
```
{
conf sys int
      edit port2
          set allowaccess ping http https ssh
          set ip 10.128.0.1/24
      end
}
```
