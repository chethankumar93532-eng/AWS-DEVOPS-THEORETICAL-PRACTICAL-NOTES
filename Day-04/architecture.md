# Day-04 — Architecture

## EC2 SSH Connectivity and Troubleshooting

```text
┌──────────────┐
│   Local Mac  │
└──────┬───────┘
       │
       │ SSH / TCP 22
       ▼
┌──────────────┐
│   Internet   │
└──────┬───────┘
       │
       ▼
┌────────────────────┐
│ Internet Gateway   │
└─────────┬──────────┘
          │
          ▼
┌────────────────────┐
│    Route Table     │
└─────────┬──────────┘
          │
          ▼
┌────────────────────┐
│       NACL         │
└─────────┬──────────┘
          │
          ▼
┌──────────────────────────────┐
│       Security Group         │
│                              │
│  Inbound: TCP 22             │
│  Source: Public IPv4 /32     │
└─────────────┬────────────────┘
              │
              ▼
┌────────────────────┐
│     EC2 Ubuntu     │
│                    │
│    SSH Service     │
└────────────────────┘
```

## Troubleshooting Flow

```text
SSH Timeout
    │
    ▼
Verify EC2 Public IP
    │
    ▼
Verify Client Public IP
    │
    ▼
Check Security Group TCP/22
    │
    ▼
Check Route Table
    │
    ▼
Check NACL
    │
    ▼
Test TCP/22 with nc
    │
    ▼
Use ssh -vvv
    │
    ▼
Authentication
    │
    ▼
Successful SSH
```

## Practical Root Cause From Day-04

The SSH timeout was traced to an **incorrect source public IP in the Security Group**. After replacing it with the correct public IPv4 `/32`, SSH connectivity was successfully established.
