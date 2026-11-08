# Architecture

This project connects the HQ and Branch enterprise LANs through a route-based IPsec VPN between two Juniper SRX gateways. The underlay is the multi-AS BGP environment documented separately in [BGP-LAB](https://github.com/emadalshishani/BGP-LAB).

## VPN Endpoints

| Site | AS | Platform | Remote peer | Tunnel interface |
|---|---:|---|---|---|
| HQ | AS100 | Juniper SRX | 10.10.40.1 | st0.0 — 100.0.0.1/30 |
| Branch | AS200 | Juniper SRX | 10.10.30.1 | st0.0 — 100.0.0.2/30 |

## Overlay Design

- The IPsec VPN is bound to `st0.0` at each endpoint.
- Static routes direct remote LAN prefixes through the tunnel interface.
- Security zones and policies control forwarded traffic.

## Underlay and Overlay Separation

- Underlay: IP reachability between IKE peer addresses through the multi-AS BGP lab.
- Overlay: encrypted traffic carried by the route-based IPsec VPN.
- Routing: static routes to remote site LAN prefixes through `st0.0`.
- Security: Junos security zones and policies.

The diagram in [topology.md](topology.md) is the project topology supplied for publication.
