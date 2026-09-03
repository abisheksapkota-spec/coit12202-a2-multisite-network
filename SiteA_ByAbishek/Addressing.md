# Site A: Config commands only

## OPNsense1
```
Option 1: WAN=vtnet0, LAN=vtnet1
Option 2:
  WAN  10.1.9.1/24   (no gateway)
  LAN  10.1.8.1/24
Interfaces > WAN: untick Block private networks, untick Block bogon networks
```

## OPNsense2
```
Option 1: WAN=vtnet0, LAN=vtnet1, OPT1=vtnet2
Option 2:
  WAN   10.1.8.2/24   (no gateway)
  LAN   10.1.10.1/24
  OPT1  10.1.20.1/24
Interfaces > WAN: untick Block private networks, untick Block bogon networks
Firewall > Rules > OPT1 > Add: Pass, source 10.1.20.0/24, destination any
```

## Host1 (Firefox Host)
```
address 10.1.10.40
netmask 255.255.255.0
gateway 10.1.10.1
```

## Host2 (kdc)
```
auto eth0
iface eth0 inet static
    address 10.1.10.10
    netmask 255.255.255.0
    gateway 10.1.10.1
    up echo nameserver 10.1.10.1 > /etc/resolv.conf
```

## Host3 (server)
```
auto eth0
iface eth0 inet static
    address 10.1.10.20
    netmask 255.255.255.0
    gateway 10.1.10.1
    up echo nameserver 10.1.10.1 > /etc/resolv.conf
```

## Host4 (client)
```
auto eth0
iface eth0 inet static
    address 10.1.10.30
    netmask 255.255.255.0
    gateway 10.1.10.1
    up echo nameserver 10.1.10.1 > /etc/resolv.conf
```

## Server1
```
auto eth0
iface eth0 inet static
    address 10.1.20.10
    netmask 255.255.255.0
    gateway 10.1.20.1
    up echo nameserver 10.1.20.1 > /etc/resolv.conf
```

## Host5
```
auto eth0
iface eth0 inet static
    address 10.1.20.20
    netmask 255.255.255.0
    gateway 10.1.20.1
    up echo nameserver 10.1.20.1 > /etc/resolv.conf
```

## VPNRouter1
```
Transit interface: 10.1.9.2/24
Internet interface: DHCP
tailscale up --advertise-routes=10.1.0.0/16
(then approve the route in the Tailscale admin console)
```

## NAT1
```
no config
```

## Hub1
```
no config
```

## IDS1
```
(blank)
```

## Host6
```
(blank)
```
