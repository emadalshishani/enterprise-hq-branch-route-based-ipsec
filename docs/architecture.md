# Architecture

The project connects HQ and Branch enterprise LANs through a route-based IPsec VPN between Juniper SRX gateways. The underlay is the multi-AS environment documented independently in [BGP-LAB](https://github.com/emadalshishani/BGP-LAB).

## VPN endpoints
| Site | AS | Device | Remote peer | Tunnel |
|---|---:|---|---|---|
| HQ | AS100 | Juniper SRX | 10.10.40.1 | st0.0 — 100.0.0.1/30 |
| Branch | AS200 | Juniper SRX | 10.10.30.1 | st0.0 — 100.0.0.2/30 |

## Overlay
- The IPsec VPN is bound to `st0.0` at both endpoints.
- Static routes direct remote LAN prefixes over the tunnel.
- Security zones and policies control forwarded traffic.

## Underlay and overlay separation
- Underlay: IP reachability between IKE peer addresses through the multi-AS BGP lab.
- Overlay: encrypted traffic carried by the route-based IPsec tunnel.
- Routing: static routes to remote site LAN prefixes using `st0.0`.
- Security: Junos security zones and policies.
