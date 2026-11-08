# Enterprise HQ–Branch Route-Based IPsec VPN

A hands-on multi-site enterprise lab implementing a route-based site-to-site IPsec VPN between two Juniper SRX gateways. The sites are connected across the multi-AS BGP environment documented separately in [BGP-LAB](https://github.com/emadalshishani/BGP-LAB).

## Topology

![Route-Based IPsec Topology](docs/images/Route-Based%20IPsec%20Topology.svg)

## Scope

- HQ SRX (AS100) to Branch SRX (AS200)
- IKEv1 Main Mode with pre-shared-key authentication
- ESP-based IPsec
- Route-based VPN using `st0.0` at both sites
- Static routes for remote LAN prefixes over the tunnel
- Security-zone and intra-zone policy handling
- IKE/IPsec SA verification
- Bidirectional LAN-to-LAN connectivity testing
- Troubleshooting using Junos flow-session inspection

## Tunnel Addressing

| Site | Interface | Address |
|---|---|---|
| HQ | st0.0 | 100.0.0.1/30 |
| Branch | st0.0 | 100.0.0.2/30 |

## Protected LAN Prefixes

| Site | Prefixes |
|---|---|
| HQ | 192.168.10.0/29, 192.168.20.0/29, 192.168.30.0/29 |
| Branch | 192.168.50.0/29, 192.168.60.0/29 |

## Security Parameters

| Parameter | Value |
|---|---|
| IKE | IKEv1 Main Mode |
| Authentication | Pre-shared key (redacted in published configs) |
| IKE encryption | AES-128-CBC |
| IKE authentication | SHA-256 |
| IKE DH group | Group 19 (updated) |
| IPsec protocol | ESP |
| IPsec encryption | AES-128-CBC |
| IPsec integrity | HMAC-SHA1-96 |
| PFS | Group 19 (updated) |

The IKE DH and IPsec PFS settings were changed from Group 5 to Group 19 on both devices. The provided IKE/IPsec SA outputs were captured before this update; fresh post-change SA detail output remains a verification item.

## Connectivity Verification

| Direction | Destination | Replies captured | Observed loss in sample |
|---|---|---:|---:|
| HQ-PC3 → Branch-PC3 | 192.168.50.3 | 27/27 | 0% |
| Branch-PC3 → HQ-PC2 | 192.168.10.3 | 22/22 | 0% |

Both ping tests were manually stopped with Ctrl+C. These are captured samples, not long-duration availability tests.

## Troubleshooting Highlight

The tunnel endpoint tests succeeded, but initial LAN-to-LAN traffic failed. Flow-session inspection showed no user LAN-to-LAN sessions in the captured output. The HQ tunnel interface was in the `internal` zone, but a matching `internal` to `internal` policy was missing. Adding that policy resolved the observed connectivity issue. Branch already had a corresponding policy.

The broad `any/any/any` rule was used as a lab troubleshooting measure. A production design should restrict source, destination, and application matches.

See [LAN-to-LAN troubleshooting](troubleshooting/lan-to-lan-connectivity.md) and [Project Report](PROJECT-REPORT.md).

## Repository Structure

```text
.
├── README.md
├── PROJECT-REPORT.md
├── configs/
│   ├── hq/
│   └── branch/
├── docs/
│   ├── architecture.md
│   ├── addressing.md
│   ├── topology.md
│   └── images/
├── testing/
└── troubleshooting/
```

## Related Project

[Multi-AS BGP Lab](https://github.com/emadalshishani/BGP-LAB)

## Publication Notes

Pre-shared keys and encrypted key material are redacted. HMAC-SHA1-96 remains in the supplied IPsec configuration and is documented as a future hardening item.
