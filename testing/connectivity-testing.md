# Connectivity Testing

## Tunnel endpoint tests
Before LAN-to-LAN policy correction:
- HQ ping to Branch tunnel IP 100.0.0.2: 2 replies from 2 transmitted.
- Branch ping to HQ tunnel IP 100.0.0.1: 4 replies from 4 transmitted.

## LAN-to-LAN tests after policy correction

| Direction | Destination | Replies captured | Observed loss in captured sample |
|---|---|---:|---:|
| HQ-PC3 → Branch-PC3 | 192.168.50.3 | 27/27 | 0% |
| Branch-PC3 → HQ-PC2 | 192.168.10.3 | 22/22 | 0% |

### HQ to Branch
- RTT range: 6.424–11.268 ms
- Calculated mean from captured replies: 7.516 ms
- No timeouts in the captured sample.

### Branch to HQ
- RTT range: 6.040–14.986 ms
- Calculated mean from captured replies: 7.125 ms
- No timeouts in the captured sample.

Both tests were manually stopped with Ctrl+C. These samples demonstrate successful bidirectional reachability during the captured intervals, not long-duration availability.
