# Addressing Plan

## Tunnel interfaces
| Site | Interface | Address |
|---|---|---|
| HQ | st0.0 | 100.0.0.1/30 |
| Branch | st0.0 | 100.0.0.2/30 |

## IKE peers
| Site | Local address | Remote peer |
|---|---:|---:|
| HQ | 10.10.30.1 | 10.10.40.1 |
| Branch | 10.10.40.1 | 10.10.30.1 |

## Site LANs
| Site | Prefix |
|---|---|
| HQ | 192.168.10.0/29 |
| HQ | 192.168.20.0/29 |
| HQ | 192.168.30.0/29 |
| Branch | 192.168.50.0/29 |
| Branch | 192.168.60.0/29 |

## Static routes
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
