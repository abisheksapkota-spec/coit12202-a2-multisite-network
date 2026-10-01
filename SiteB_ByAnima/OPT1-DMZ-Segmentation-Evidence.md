# Site B - OPT1 DMZ Segmentation Evidence

**Site:** B  
**Owner:** Anima Parajuli  
**Firewall:** OPNsense-B  
**DMZ Interface:** OPT1

## Objective

The purpose of this configuration is to provide network segmentation between the Site B DMZ and the internal LAN.

The Site B networks used in this test are:

- **DMZ:** `10.2.20.0/24`
- **LAN:** `10.2.10.0/24`
- **DMZ test host (web-B):** `10.2.20.10`
- **LAN test host:** `10.2.10.40`

## Firewall Rule

An explicit block rule was configured on the OPNsense OPT1 interface.

Configuration:

- **Action:** Block
- **Interface:** OPT1
- **Direction:** In
- **TCP/IP Version:** IPv4
- **Protocol:** Any
- **Source:** `10.2.20.0/24`
- **Destination:** `10.2.10.0/24`
- **Logging:** Enabled
- **Description:** `Explicit deny DMZ -> LAN - zone segmentation`

The block rule was positioned above the general DMZ allow rule so that the specific DMZ-to-LAN restriction is evaluated first.

## Live Test

The firewall rule was tested from `web-B`, located in the Site B DMZ.

The following command was used:

`ping -c 4 10.2.10.40`

The ping from the DMZ host to the LAN host was unsuccessful.

## Firewall Log Verification

After generating the ICMP traffic, OPNsense **Firewall > Log Files > Live View** was checked.

The Live View was filtered for blocked traffic using:

`action contains block`

Red block entries were displayed for the attempted DMZ-to-LAN traffic.

This confirmed that OPNsense was actively blocking and logging the traffic rather than the ping failing only because of an unrelated connectivity problem.

## Security Purpose

This rule provides separation between the public-facing DMZ and the protected internal LAN.

If a service or host in the DMZ is compromised, the firewall restriction helps prevent that system from directly accessing hosts located on the Site B internal LAN.

## Result

The Site B OPT1 segmentation test was successful.

Traffic generated from the Site B DMZ toward the internal LAN was blocked and logged by OPNsense, demonstrating enforcement of the configured DMZ-to-LAN security boundary.
