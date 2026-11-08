# Project Report — Enterprise HQ–Branch Route-Based IPsec VPN

## Objective

Implement and verify a route-based site-to-site IPsec VPN between HQ and Branch Juniper SRX gateways across the multi-AS BGP underlay documented in a separate repository.

## VPN Endpoints

| Site | Local peer address | Remote peer address | Tunnel interface |
|---|---:|---:|---|
| HQ | 10.10.30.1 | 10.10.40.1 | st0.0 — 100.0.0.1/30 |
| Branch | 10.10.40.1 | 10.10.30.1 | st0.0 — 100.0.0.2/30 |

## Cryptographic Configuration

| Parameter | Configured value |
|---|---|
| IKE | IKEv1 Main Mode |
| Authentication | Pre-shared keys |
| IKE encryption | AES-128-CBC |
| IKE authentication | SHA-256 |
| IKE DH group | Group 19 (updated) |
| IPsec protocol | ESP |
| IPsec encryption | AES-128-CBC |
| IPsec integrity | HMAC-SHA1-96 |
| IPsec PFS | Group 19 (updated) |

Both endpoints were changed from DH/PFS Group 5 to Group 19. The supplied SA outputs showing UP were captured before the Group 19 change. Capture fresh SA details after re-negotiation to verify the post-change negotiated parameters.

## Route-Based VPN and Static Routing

The IPsec VPN is bound to `st0.0` at both sites.

HQ routes toward Branch:

```text
192.168.50.0/29 next-hop st0.0
192.168.60.0/29 next-hop st0.0
```

Branch routes toward HQ:

```text
192.168.10.0/29 next-hop st0.0
192.168.20.0/29 next-hop st0.0
192.168.30.0/29 next-hop st0.0
```

The remote tunnel endpoint IP was also tested as a static-route next hop, but did not resolve the observed LAN-to-LAN issue.

## Security Zones and Policies

The tunnel interface was moved into the `internal` zone at both sites. The previous HQ `branch` and Branch `hq` memberships were removed.

On HQ, an `internal` to `internal` policy matching any source, destination, and application was added during troubleshooting. Branch already had a corresponding policy. This broad policy is a lab troubleshooting measure, not a production recommendation.

## Verification

Initial tunnel endpoint tests:
- HQ ping to 100.0.0.2: 2 replies from 2 transmitted.
- Branch ping to 100.0.0.1: 4 replies from 4 transmitted.
- IKE SA state UP and one IPsec SA were shown in the captured output before Group 19 was applied.

Post-fix LAN-to-LAN samples:

| Direction | Destination | Replies captured | Observed loss in sample |
|---|---|---:|---:|
| HQ-PC3 → Branch-PC3 | 192.168.50.3 | 27/27 | 0% |
| Branch-PC3 → HQ-PC2 | 192.168.10.3 | 22/22 | 0% |

Both tests began receiving replies at sequence 1 and showed no timeouts in the captured samples. They were manually stopped with Ctrl+C and do not establish long-term availability.

## Troubleshooting Summary

1. IKE/IPsec SAs and tunnel endpoint reachability were verified.
2. Initial LAN-to-LAN traffic failed.
3. Static routes and alternate next-hop syntax were reviewed/tested.
4. The HQ flow-session output contained BGP TCP/179 and ESP-related entries, but no user LAN-to-LAN sessions in the captured output.
5. The HQ tunnel interface was in the `internal` zone, but a matching intra-zone permit policy was missing.
6. An HQ `internal` to `internal` permit policy was added. Branch already had a corresponding policy.
7. Bidirectional LAN-to-LAN ping samples then showed successful replies without captured timeouts.

The evidence supports the missing HQ intra-zone policy as the corrective action in this scenario.

## Future Improvements

- Capture fresh IKE/IPsec SA details after Group 19 update.
- Verify installed routes and next-hop resolution.
- Replace broad policies with least-privilege rules.
- Consider a dedicated VPN security zone with explicit policies.
- Evaluate a stronger IPsec integrity algorithm supported by the platform.
- Perform longer-duration testing and controlled tunnel failure/recovery tests.
