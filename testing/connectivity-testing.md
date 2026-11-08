# Connectivity Testing

## Tunnel Endpoint Reachability

Before the LAN-to-LAN policy correction:
- HQ ping to Branch tunnel IP 100.0.0.2: 2 replies from 2 transmitted.
- Branch ping to HQ tunnel IP 100.0.0.1: 4 replies from 4 transmitted.

## LAN-to-LAN Connectivity After Policy Correction

| Direction | Destination | Replies captured | Observed loss in captured sample |
|---|---|---:|---:|
| HQ-PC3 → Branch-PC3 | 192.168.50.3 | 27/27 | 0% |
| Branch-PC3 → HQ-PC2 | 192.168.10.3 | 22/22 | 0% |

### HQ to Branch
- RTT range: 6.424–11.268 ms
- Calculated mean: 7.516 ms
- No timeouts appeared in the captured sample.

### Branch to HQ
- RTT range: 6.040–14.986 ms
- Calculated mean: 7.125 ms
- No timeouts appeared in the captured sample.

Both tests were manually stopped with Ctrl+C. These captured samples demonstrate bidirectional reachability during the test intervals, not long-duration availability.
