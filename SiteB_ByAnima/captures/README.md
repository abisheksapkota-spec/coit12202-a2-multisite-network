# Site B Captures and Connectivity Evidence

## Site B to Site C Connectivity Test

This folder contains connectivity evidence for Site B.

A packet capture was intended to be collected using the GNS3 packet capture feature and Wireshark. However, during testing, the packet capture environment was not functioning correctly and Wireshark could not be used successfully. Therefore, a `.pcap` capture could not be produced.

As alternative supporting evidence, a live connectivity test was performed from the Site B client to Site C.

### Test Details

- **Source:** Site B Client
- **Destination:** Site C
- **Destination IP:** `10.3.9.2`
- **Protocol:** ICMP

### Command Used

`ping 10.3.9.2`

### Result

The connectivity test was successful:

- 5 packets transmitted
- 5 packets received
- 0% packet loss

The successful ICMP replies demonstrate that Site B was able to reach the Site C network during the test.

A screenshot of the successful test is included in this folder as:

`SiteB-to-SiteC-Ping-Success.png`

## Packet Capture Limitation

A packet-level `.pcap`/`.pcapng` file is not included because the GNS3/Wireshark packet capture setup was not functioning correctly during the test.

The included ping screenshot is provided as connectivity evidence only and is not presented as a replacement for a packet-level capture.
