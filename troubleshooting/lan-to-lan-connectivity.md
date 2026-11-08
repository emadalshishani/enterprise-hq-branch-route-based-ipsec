# Troubleshooting: LAN-to-LAN Connectivity

## Problem
The route-based IPsec tunnel was established and tunnel endpoint pings succeeded, but end devices at HQ and Branch initially could not communicate.

## Initial static routes
Branch to HQ:
```text
192.168.10.0/29 → st0.0
192.168.20.0/29 → st0.0
192.168.30.0/29 → st0.0
```

HQ to Branch:
```text
192.168.50.0/29 → st0.0
192.168.60.0/29 → st0.0
```

Using the opposite tunnel endpoint address (100.0.0.1 / 100.0.0.2) as the static route next hop was also tested, but did not resolve the issue.

## Flow-session investigation
The captured HQ flow table showed:
- An established TCP/179 session associated with BGP.
- ESP-related entries for the IPsec peer.
- No user LAN-to-LAN sessions in the captured output.

## Security-zone finding
The tunnel interface had been moved into the `internal` zone on both SRX devices, and its previous `branch` / `hq` zone memberships were removed. HQ did not yet have an `internal` to `internal` policy matching the forwarded traffic. Branch already had a corresponding policy configured previously.

## Corrective action on HQ
```text
set security policies from-zone internal to-zone internal policy internal-internal match source-address any
set security policies from-zone internal to-zone internal policy internal-internal match destination-address any
set security policies from-zone internal to-zone internal policy internal-internal match application any
set security policies from-zone internal to-zone internal policy internal-internal then permit
```

After adding this policy, bidirectional LAN-to-LAN connectivity began working.

## Post-fix verification
- HQ-PC3 to 192.168.50.3: 27 replies captured; no timeouts in the sample.
- Branch-PC3 to 192.168.10.3: 22 replies captured; no timeouts in the sample.
- Both tests were stopped manually with Ctrl+C.

## Root-cause assessment
The missing HQ intra-zone permit policy was the corrective action that resolved the observed issue, based on the troubleshooting sequence and the subsequent successful tests.

## Lessons learned
- A healthy IKE/IPsec control plane does not alone prove end-to-end LAN connectivity.
- Verify routes to remote LAN prefixes on both gateways.
- Inspect flow sessions when forwarded traffic is not behaving as expected.
- Check the actual ingress and egress zones and whether a matching security policy exists.
- The any/any/any policy was a broad lab troubleshooting measure. Production policies should apply least privilege.
