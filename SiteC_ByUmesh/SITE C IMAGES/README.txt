SITE C BY UMESH - FINAL GITHUB EVIDENCE

01 Final GNS3 Site C topology
02 IPsec Site A tunnel established
03 IPsec SPD policies
04 ESP encrypted traffic leaving Site C
05 IPsec status/outbound traffic evidence
06 Kerberos KDC database and principals
07 Kerberos KDC ticket success
08 client-C Kerberos authentication success
09 server-C keytab authentication success
10 Suricata fresh ICMP alert success
11 Final Site C IPsec SPD policies

Important VPN note:
Site C establishes the IPsec tunnel and sends encrypted ESP traffic. At the final test, return traffic from the remote site was still unresolved (outbound bytes present, inbound bytes 0), so do not describe end-to-end Site A LAN connectivity as successful.
evidence/
└── Site-C-Umesh/
    ├── 01_SiteC_GNS3_Final_Topology.png
    ├── ...
    └── 11_SiteC_IPsec_SPD_Policies_Final.png
