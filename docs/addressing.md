# Addressing Plan

## Tunnel Interfaces

| Site | Interface | Address | Purpose |
|---|---|---|---|
| HQ | st0.0 | 100.0.0.1/30 | IPsec tunnel |
| Branch | st0.0 | 100.0.0.2/30 | IPsec tunnel |

## IKE Peer Addresses

| Site | Local address | Remote peer |
|---|---:|---:|
| HQ | 10.10.30.1 | 10.10.40.1 |
| Branch | 10.10.40.1 | 10.10.30.1 |

## Site LAN Prefixes

| Site | Prefix |
|---|---|
| HQ | 192.168.10.0/29 |
| HQ | 192.168.20.0/29 |
| HQ | 192.168.30.0/29 |
| Branch | 192.168.50.0/29 |
| Branch | 192.168.60.0/29 |

## Static Routes

HQ:
```text
192.168.50.0/29 next-hop st0.0
192.168.60.0/29 next-hop st0.0
```

Branch:
```text
192.168.10.0/29 next-hop st0.0
192.168.20.0/29 next-hop st0.0
192.168.30.0/29 next-hop st0.0
```
