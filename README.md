# Enterprise HQ–Branch Route-Based IPsec VPN

A hands-on multi-site enterprise lab implementing a route-based site-to-site IPsec VPN between two Juniper SRX gateways. The sites are connected across the multi-AS BGP environment documented separately in [BGP-LAB](https://github.com/emadalshishani/BGP-LAB).

## Topology

![Route-Based IPsec Topology](docs/images/Route-Based%20IPsec%20Topology.svg)

## Project Scope

- HQ SRX (AS100) to Branch SRX (AS200)
- IKEv1 Main Mode with pre-shared-key authentication
- ESP-based IPsec using AES-128-CBC and HMAC-SHA1-96
- Group 19 for IKE DH and IPsec PFS (updated configuration)
- Route-based VPN using `st0.0` at both sites
- Static routes for remote LAN prefixes over the tunnel
- Security-zone policy handling
- IKE/IPsec SA verification and bidirectional LAN-to-LAN testing
- Flow-session troubleshooting

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

## Verification Summary

| Direction | Destination | Replies captured | Observed loss in captured sample |
|---|---|---:|---:|
| HQ-PC3 → Branch-PC3 | 192.168.50.3 | 27/27 | 0% |
| Branch-PC3 → HQ-PC2 | 192.168.10.3 | 22/22 | 0% |

The ping tests were manually stopped with Ctrl+C. The results describe the captured samples, not a long-duration availability test. The IKE/IPsec SA output in the repository was captured before the later Group 19 update; fresh post-change SA detail output remains a verification item.

## Troubleshooting Highlight

The tunnel endpoint tests succeeded, but initial LAN-to-LAN traffic failed. Flow-session inspection showed no user LAN-to-LAN sessions in the captured output. The HQ tunnel interface was in the `internal` zone, but a matching `internal` to `internal` permit policy was missing. Adding that policy resolved the observed connectivity issue. Branch already had a corresponding policy.

The lab policy uses `any` for source, destination, and application matching as a troubleshooting measure. Replace it with least-privilege address and application matches in a production design.

See [troubleshooting notes](troubleshooting/lan-to-lan-connectivity.md), [project report](PROJECT-REPORT.md), and [connectivity testing](testing/connectivity-testing.md).

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
│   ├── topology.md
│   ├── addressing.md
│   └── images/
├── testing/
└── troubleshooting/
```

## Related Project

[Multi-AS BGP Lab](https://github.com/emadalshishani/BGP-LAB)

## Publication Notes

Pre-shared keys and encrypted key material are redacted. HMAC-SHA1-96 remains in the supplied IPsec configuration and is listed as a future hardening item.
