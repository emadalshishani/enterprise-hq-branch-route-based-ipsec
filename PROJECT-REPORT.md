# Project Report — Enterprise HQ–Branch Route-Based IPsec VPN

## Objective
Implement and verify a route-based site-to-site IPsec VPN between HQ and Branch Juniper SRX gateways across the separate multi-AS BGP underlay.

## Endpoints and tunnel
| Site | Peer address | Tunnel interface |
|---|---|---|
| HQ | 10.10.40.1 | st0.0 — 100.0.0.1/30 |
| Branch | 10.10.30.1 | st0.0 — 100.0.0.2/30 |

## Security parameters
| Parameter | Value |
|---|---|
| IKE | IKEv1 Main Mode |
| Authentication | Pre-shared key (redacted) |
| IKE encryption | AES-128-CBC |
| IKE authentication | SHA-256 |
| IKE DH group | Group 19 (updated) |
| IPsec protocol | ESP |
| IPsec encryption | AES-128-CBC |
| IPsec integrity | HMAC-SHA1-96 |
| IPsec PFS | Group 19 (updated) |

The original DH/PFS configuration used Group 5 and was later changed to Group 19 at both ends. The captured UP SA output in this repository predates that change; a fresh post-change SA detail capture is still required.

## Routing
HQ routes to Branch:
```text
192.168.50.0/29 next-hop st0.0
192.168.60.0/29 next-hop st0.0
```

Branch routes to HQ:
```text
192.168.10.0/29 next-hop st0.0
192.168.20.0/29 next-hop st0.0
192.168.30.0/29 next-hop st0.0
```

The remote tunnel endpoint IP as a static route next hop was also tested during troubleshooting but did not resolve the issue.

## Security zones and policies
The tunnel interface was moved to the `internal` zone at both endpoints. The previous zone memberships (`branch` on HQ and `hq` on Branch) were removed.

On HQ, an `internal` to `internal` permit policy matching any source, destination, and application was added during troubleshooting. Branch already had a corresponding policy. This broad policy is documented as a lab troubleshooting action, not a production recommendation.

## Verification
Initial tunnel endpoint tests:
- HQ to 100.0.0.2: 2 replies from 2 transmitted.
- Branch to 100.0.0.1: 4 replies from 4 transmitted.
- IKE SA and IPsec SA were UP in the captured output before Group 19 was applied.

Post-fix LAN-to-LAN samples:
| Direction | Destination | Replies captured | Observed loss in sample |
|---|---|---:|---:|
| HQ-PC3 → Branch-PC3 | 192.168.50.3 | 27/27 | 0% |
| Branch-PC3 → HQ-PC2 | 192.168.10.3 | 22/22 | 0% |

Both ping tests were stopped manually with Ctrl+C. These samples do not establish long-term availability or throughput.

## Troubleshooting summary
1. Tunnel endpoint reachability and IKE/IPsec SAs were verified.
2. End-host traffic initially failed.
3. Static routes and alternate next-hop syntax were reviewed/tested.
4. The HQ flow session table showed BGP TCP/179 and ESP entries, but no user LAN-to-LAN sessions in the captured output.
5. The tunnel interface was in HQ's `internal` zone, but no matching intra-zone policy was configured.
6. An HQ `internal` to `internal` permit policy was added. Branch already had a corresponding rule.
7. Bidirectional LAN-to-LAN ping samples then showed replies from the first captured sequence without timeouts.

## Future improvements
- Capture fresh IKE/IPsec SA details after Group 19 update.
- Verify installed static routes and next-hop resolution.
- Replace broad rules with least-privilege policies.
- Consider a dedicated VPN zone and explicit inter-zone policy.
- Evaluate a stronger IPsec integrity algorithm supported by the platform.
- Perform longer-duration testing and controlled tunnel failure/recovery tests.
