# Day-06 Architecture — AWS Network Firewall & RDP

```text
YOUR MAC / RDP CLIENT
        |
        | TCP 3389
        v
 PUBLIC INTERNET
        |
        v
 INTERNET GATEWAY
        |
        v
+---------------------------+
|       PROTECTED VPC       |
|                           |
|  +---------------------+  |
|  |   Firewall Subnet   |  |
|  | AWS Network Firewall|  |
|  +----------+----------+  |
|             |               |
|       GWLB Endpoint         |
|             |               |
|  +----------v----------+    |
|  |  Protected Subnet   |    |
|  | Windows Server 2025 |    |
|  |        EC2          |    |
|  +---------------------+    |
+---------------------------+
```

### Stateful Rules Practiced
- SSH — TCP 22
- RDP — TCP 3389
- HTTP — TCP 80

### RDP Troubleshooting Flow
```text
RDP Error 0x4
 → EC2 status
 → RDP configuration
 → Route tables
 → GWLB Endpoint
 → Network Firewall
 → TCP 3389 test with nc
 → Verify source public IP
 → Correct to current Public IP /32
 → RDP success
```
